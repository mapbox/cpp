## Using strong types in c++

Author: https://github.com/artemp

##### Bite-sized c++ design patterns 

Here is a `bite-size` c++ design pattern - "Phantom types" or also known as ghost types, strong types, BOOST_STRONG_TYPEDEF etc.

#### Introduction: what's it all about?

The easiest way to describe this pattern is by example from basic science. Consider Newton's second law:

`
                                        F = m * a
`
                                        
Where `F` is force, `m` is mass and `a` is acceleration. All these parameters can be represented by floating point types.

```c++
double m = 100.0; // mass
double a = 9.8; // acceleration

double calc_force(double m, double a)
{
    return m * a;
}

double F = calc_force(m, a); // unbalanced force
```

All good and sound as far as relations between these types and units are preserved.  This means variable `m` is representing mass, `a` is acceleration and so on. This is a simplistic example, but in larger, more complex code the problem arises when operations on unrelated types can lead to hard to spot logical bugs. Think about a nuclear plant and a careless programmer who caused a meltdown by plucking wrong types into equation.  There's nothing in the code above tells compiler that `m` is mass - it's just `double`. Luckily, c++ being a strongly typed programming language, allows us to be explicit about types involved. When we write c++, compiler is our main target audience!!


```c++
mass m = 100.0;
acceleration = 9.8;
force calc_force(mass m, acceleration a)
{
    return m * a;
}
force F = calc_force(m, a);
```

Note that there's no need for comments anymore:). But more importantly we're communicating directly and explicitly to the compiler that to get `force` we need to multiply `mass` and `acceleration` and not anything else. Following code, for example,  would result in compile error:

```c++
mass m = 100.0;
mass a = 10.0;
force = F = calc_force(m, a); // Compile ERROR!
```

Jumping into our domain:

```c++

struct vector_tile
{
    // some internal state
    // ...
    //
    bool processed;
};

vector_tile create_vector_tile(...);

void do_something_with_processed_vector_tile(vector_tile const& tile);
// this method expects `processed' vector tile but there's nothing stopping someone making a mistake
```

Ok, we can write validation code inside methods body :

```c++
void do_something_with_processed_vector_tile(vector_tile const& tile)
{
    if (!tile.processed) return;
    // Do some stuff with the tile
}
```

But `c++` provides much better way to enforce correct symantics at COMPILE time!!!

```c++
struct vector_tile {};
struct processed_vector_tile {};

processed_vector_tile do_some_processing(vector_tile const& tile);
void do_something_with_processed_vector_tile(processed_vector_tile const& tile);

```

Note how methods signatures changed ^. It's now a compile error to pass anything but `processed_vector_tile` to `do_something_with_processed_vector_tile`.

#### Now to the main point

Everyone should be convinced by now of the usefulness of this pattern, hopefully.

Strong types in c++ are useful for enforcing correct units, validation, deterministic calculations.

Let's discuss possible implementations. Let's try making using this pattern as easy as possible. Should we use existing `BOOST_STRONG_TYPEDEF` ?
Or should we implement our own simple `header` only library. This is the main topic of this workshop.


#### Using BOOST_STRONG_TYPEDEF

```c++
#include <boost/serialization/strong_typedef.hpp>
#include <iostream>

BOOST_STRONG_TYPEDEF(int, apples);
BOOST_STRONG_TYPEDEF(int, oranges);

void print_apples( apples const& v)
{
    std::cerr << v << std::endl;
}

int main()
{

    apples v0 {123};
    oranges v1 {456};

    print_apples(v0); // OK
    //print_apples(v1); // Compile ERROR
    return 0;
}
```


#### Custom implementation

```c++
#ifndef PHANTOM_TYPE_HPP
#define PHANTOM_TYPE_HPP

#include <utility>


namespace util {

template <typename BaseType, typename TypeName>
struct phantom_type
{
    explicit constexpr phantom_type(BaseType const& value) : value(value) {}
    explicit constexpr phantom_type(BaseType&& value) : value(std::move(value)) {}
    operator BaseType& () noexcept { return value; }
    constexpr operator BaseType const& () const noexcept { return value; }
    BaseType value;
};
} // namespace

#define PHANTOM_TYPE(BASE_TYPE, TYPE_NAME)                   \
struct TYPE_NAME : util::phantom_type<BASE_TYPE, TYPE_NAME>  \
{                                                            \
    using phantom_type::phantom_type;                        \
};

#endif // PHANTOM_TYPE_HPP

```

##### Using it

```c++
#include "phantom_type.hpp"

#include <iostream>

PHANTOM_TYPE(int, apples);
PHANTOM_TYPE(int, oranges);

void print_apples( apples const& v)
{
    std::cerr << v << std::endl;
}

int main()
{

    apples v0{123};
    oranges v1{456};

    print_apples(v0); // OK
    //print_apples(v1); // Compile ERROR
    return 0;
}
```

#### Performance considerations 

