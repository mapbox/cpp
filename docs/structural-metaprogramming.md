# C++ Structural Metaprogramming

Have you ever wished you could:

* Iterate over the member variables of a class or structure?
* Apply a transform to the type of each member, to produce a new list of members?
* Define a family of classes, each with a different set of members, but behaving in a common way?

If so, you probably ran into the fact that compile-time metaprogramming over member variables has historically been a weak point of C++. I thought so too. But recently I've discovered a few techniques for dealing with these kind of situations that work pretty well in practice, and I wanted to share them.

### Super `std::tuple`

The basis for this technique is `std::tuple`, and the essential idea is that instead of using a `struct` or `class` type with regular members, we use a type that derives from `std::tuple`, where what would normally be member variables are instead `std::tuple` template parameters.

To make this concrete, let's take [an example from Mapbox GL](https://www.mapbox.com/mapbox-gl-style-spec/#layers-circle). Suppose we have a circle layer type with properties such as `circle-color`, `circle-opacity`, and `circle-radius`. The value of each property is a function returning a type appropriate to that property, evaluated at a particular zoom level. We'd also like to define a function that evaluates _all_ the properties of a layer at a particular zoom level.

Traditionally, we would probably reach for a solution like the following:

```C++
struct EvaluatedCircleProperties {
	Color color;
	float opacity;
	float radius;
};

struct UnevaluatedCircleProperties {
    std::function<Color (float)> color;
    std::function<float (float)> opacity;
    std::function<float (float)> radius;
    
    EvaluatedCircleProperties evaluate(float zoom) const {
        return {
            color(zoom),
            opacity(zoom),
            radius(zoom)
        };
    }
};
```

This works well enough for a simple three-property example, but it has a few disadvantages:

* We repeat the property types in three places. If we were to add a property, we'd have to update them all.
* When you have multiple layer types, you also have to repeat the "superstructure" -- the knowledge that each layer has `Evaluated` and `Unevaluated` structs with corresponding members, and that in each case `evaluate` is defined by evaluating member-wise and aggregate-constructing the result.
* These costs increase as the number of properties and layer types grow.

Here's an alternative:

```C++
template <class... Ps>
struct Properties {
    struct Evaluated : std::tuple<Ps...> {};
    struct Unevaluated : std::tuple<std::function<Ps (float)>...> {
        Evaluated evaluate(float zoom) const;
    };
};

struct ColorProperties : Properties<
	Color,
	float,
	float> {};
```

Instead of using a member variable for each property, we use a template parameter. Now adding a new property requires a code change in only one place. We've also defined the structure and relationship between `Evaluated` and `Unevaluated` sets of properties in a single place; no need to repeat this for other layer types.

But I've left out some details. First, how do we access an individual value in the `Evaluated` and `Unevaluated` structs -- radius, say? With member variables, we can use member variable syntax: `properties.radius`. But tuple members are unnamed; the only way to access them is with [`std::get`](http://en.cppreference.com/w/cpp/utility/tuple/get). And because multiple properties may have the same type, we can't even use the type-based `std::get`; `std::get<float>(properties)` could mean either opacity or radius. We're stuck with `std::get<2>(properties)`, which has terrible readability and would break if we reordered the properties or inserted a new one before radius.

We can solve this problem if we ensure that each template parameter has a unique type:

```C++
template <class T>
struct Property {
    using EvaluatedType = T;
    using UnevaluatedType = std::function<T (float)>;
};

template <class... Ps>
struct Properties {
    struct Evaluated : std::tuple<typename Ps::EvaluatedType...> {
        template <class P> auto get();
    };
    
    struct Unevaluated : std::tuple<typename Ps::UnevaluatedType...> {
        template <class P> auto get();
        Evaluated evaluate(float zoom);
    };
};

struct CircleColor : Property<Color> {};
struct CircleOpacity : Property<float> {};
struct CircleRadius : Property<float> {};

struct ColorProperties : Properties<
    CircleColor,
    CircleOpacity,
    CircleRadius> {};
```

We've added a `get` member function to `Evaluated` and `Unevaluated`, which provides a much nicer syntax: `properties.get<CircleRadius>()`. The cost is that we now need to define a new type for each property, touching two places rather than one. But I think it's worth it -- and in fact this case having a distinct type is convenient, as it provides a place to hang other per-property data such as the default value.

### More `tuple` tricks

I haven't shown the implementation of `get` or `evaluate` yet. They involve a few more tricks. First, `get`:

```C++
template <typename T, typename... Ts>
struct TypeIndex;

template <typename T, typename... Ts>
struct TypeIndex<T, T, Ts...> : std::integral_constant<std::size_t, 0> {};

template <typename T, typename U, typename... Ts>
struct TypeIndex<T, U, Ts...> : std::integral_constant<std::size_t, 1 + TypeIndex<T, Ts...>::value> {};

template <class... Ps>
struct Properties {
    template <class P>
    static constexpr std::size_t Index = TypeIndex<P, Ps...>::value;

    struct Evaluated : std::tuple<typename Ps::EvaluatedType...> {
        template <class P> auto get() {
            return std::get<Index<P>>(*this);
        }
    };
    
    ...
};
```

Why this implementation? Well, we want to allow `properties.get<CircleRadius>()` where `properties` is either `Evaluated` or `Unevaluated`. However, `CircleRadius` is not itself one of the `Evaluated` or `Unevaluated` tuple template parameters. What `get` needs to do is obtain the _index_ of `CircleRadius` in the `Ps` template parameter pack, and then use that index as the template parameter for `std::get`. That's what `TypeIndex` and `Properties::Index` accomplish. (`Properties::Index` uses a [variable template](http://en.cppreference.com/w/cpp/language/variable_template), a C++14 feature, but you could replace its uses with `TypeIndex<P, Ps...>::value` for a pure C++11 solution.)

Now, what about `evaluate`? Here's its definition:

```C++
template <class... Ps>
struct Properties {
    ...

    struct Unevaluated : std::tuple<typename Ps::UnevaluatedType...> {
        template <class P> auto get() { ... }
        
        Evaluated evaluate(float zoom) {
            return { get<Ps>()(zoom)... };
        }
    };
    
    ...
};
```

🤗 Our definition of `get` turns out to be very useful, as we can now use template parameter pack expansion over `Ps` to automatically evaluate all the properties, no matter how many of them there are.

### How to ignore all the things

One last trick. We were able to take advantage of parameter pack expansion in the above definition because we conveniently had a context -- aggregate construction -- in which expansion with `...` is allowed. What if we wanted to define a member-wise operation where we wouldn't normally have such a context?

For example, suppose we need to check if any of the unevaluated `std::function`s are [empty](http://en.cppreference.com/w/cpp/utility/functional/function/operator_bool). We need to calculate `bool(color) || bool(opacity) || bool(radius) || ...`, but defined generically over the parameter pack `Ps`. In C++17 we'll be getting [fold expressions](http://en.cppreference.com/w/cpp/language/fold) for this sort of thing, but we can simulate them in C++ today:

```C++
template <class... Ts> void ignore(Ts&&...) {}

template <class... Ps>
bool any_empty(const Properties<Ps...>::Unevaluated& properties) {
    bool result = false;
    ignore((result |= bool(properties.get<Ps>()))...);
    return result;
}
```

In order to give ourselves a context in which the `|=` expression can be expanded, we define the `ignore` template function, which takes any number of arguments of any types and does nothing with them. We expand the parameter pack into arguments to `ignore`, relying on the expression's side effect to compute the desired `result`.

Using `ignore`, you can expand pretty much any expression. For expressions that would normally be typed `void`, which isn't allowed as a function parameter type, use a comma expression instead: e.g. `ignore((fnWithVoidResultType(get<Ps>()), 0)...)`.

In summary: `std::tuple` and C++ template parameter packs are super powerful, and with this one weird trick (ok, several weird tricks), very useful for structural metaprogramming.