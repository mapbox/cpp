# UDL (user defined literals in c++)

## Background

### What is a literal ?

You should know the answer already, right ? ;)

via Wikipedia:

"In computer science, a literal is a notation for representing a fixed value in source code. Almost all programming languages have notations for atomic values such as integers, floating-point numbers, and strings, and usually for booleans and characters; some also have notations for elements of enumerated types and compound values such as arrays, records, and objects. An anonymous function is a literal for the function type."


### Integer literals in c++
Allows values of integer type to be used in expressions directly.

```c++
int d = 42;
int o = 052;
int x = 0x2a;
int X = 0X2A;
int b = 0b101010; // c++14
```


### string literals

* " (unescaped_character|escaped_character)* "	(1)
* L " (unescaped_character|escaped_character)* "	(2)
* u8 " (unescaped_character|escaped_character)* "	(3)	(since C++11)
* u " (unescaped_character|escaped_character)* "	(4)	(since C++11)
* U " (unescaped_character|escaped_character)* "	(5)	(since C++11)
* prefix(optional) R "delimiter( raw_characters )delimiter"	(6)	(since C++11)

### Floating point literals

```
1.23e-12
```
More in your favourite c++ reference...

### User defined literals UDL `*NEW from c++11!*`

UDLs have been part of the standard from c++11:
http://en.cppreference.com/w/cpp/language/user_literal

"Allows integer, floating-point, character, and string literals to produce objects of user-defined type by defining a user-defined suffix."


## Contrived example

Let's say we'd like to craft a data structure for representing geographical points. Something like this:

```c++
template <typename T>
struct geographic_coordinate
{
    using type = T;
    type value;
    explicit constexpr geographic_coordinate(T value_)
        : value(value_)
    {}
    // operator T() const { return value;}
    // geographic_coordinate<T>& operator=(geographic_coordinate && other) = default;
};

```

We also would like to clearly differentiate between longitude and latitude. Both are geographical coordinates and both share the same base class.

```c++

template <typename T>
struct longitude : geographic_coordinate<T>
{
    using geographic_coordinate<T>::geographic_coordinate;
};

template <typename T>
struct latitude : geographic_coordinate<T>
{
    using geographic_coordinate<T>::geographic_coordinate;
};

```

To reduce verbosity we add a couple of type definitions aliases. Also we add streaming operator to be able to check underlying values:

```c++
using LON = longitude<double>;
using LAT = latitude<double>;

template <typename T>
std::ostream & operator<<(std::ostream & out, geographic_coordinate<T> const& p)
{
    out << p.value;
    return out;
}

```

With this framework in hand we can now go ahead and implement `geographic_point` structure which can be constructed from longitude and latitude. We'd like to be able to write `geographic_point(lan, lon)` or `geographic_point(lon, lat)`, both resulting in the same object. To accomplish this we define two specialised constructors:

```c++

template <typename T>
struct geographic_point
{
    constexpr geographic_point(longitude<T> lon, latitude<T> lat)
        : lon_(lon.value),
          lat_(lat.value) {}

    constexpr geographic_point(latitude<T> lat, longitude<T> lon)
        : lon_(lon.value),
          lat_(lat.value) {}

    T lon_;
    T lat_;
};

```

Almost, done! We will also need an output streaming operator and `equal/not-equal` operators

```c++
template <typename T>
constexpr bool operator==(geographic_point<T> const& p0, geographic_point<T> const& p1)
{
    return (p0.lon_ == p1.lon_) && (p0.lat_ == p1.lat_);
}

template <typename T>
constexpr bool operator!=(geographic_point<T> const& p0, geographic_point<T> const& p1)
{
    //return !(p0.lon_ == p1.lon_) && (p0.lat_ == p1.lat_);
    //return (p0.lon_ != p1.lon_) || (p0.lat_ != p1.lat_);
    return !(p0 == p1);
}

template <typename T>
std::ostream & operator<<(std::ostream & out, geographic_point<T> const& p)
{
    out << "GeoPoint(" << p.lon_ << " lon," << p.lat_ << " lat)";
    return out;
}

```

We can now create our points like this:

```c++
constexpr geographic_point<double> p(LON(-1.2), LAT(51));
```

or

```c++
constexpr geographic_point<double> p(LAT(51), LON(-1.2));
```

Following attempt to construct a point will result in compile error as expected:

```c++
//constexpr geographic_point<double> p(52.0, -2.0);     // compile error
```

Ok, but what about UDLs :) ? Let's say we'd like to have a concise way to define geographical coordinates at compile time. Something like  `-1.2_lon` `51_lat` for example.

To achieve this we define:

```c++

constexpr longitude<double> operator ""_lon(long double v)
{
    return longitude<double>{static_cast<double>(v)};
}

constexpr latitude<double> operator ""_lat(long double v)
{
    return latitude<double>{static_cast<double>(v)};
}

```

Note, that we use `long double` for the argument type. This is because a numeric argument type must have the largest possible representation. In case of a floating point type it's `long double`. So far so good, but there are more things to do. Firstly, we also need to add overloads for integer arguments. Without them the compiler won't accept `51_lat`.


```c++
constexpr longitude<double> operator ""_lon(unsigned long long v)
{
    return longitude<double>{static_cast<double>(v)};
}

constexpr latitude<double> operator ""_lat(unsigned long long v)
{
    return latitude<double>{static_cast<double>(v)};
}

```

Secondly, `-` minus sign is not a part of a literal and to be able to construct negative longitude and latitide we must provide unary `operator-()`. Here are our updated definitions:


```c++

template <typename T>
struct longitude : geographic_coordinate<T>
{
    using geographic_coordinate<T>::geographic_coordinate;
    constexpr longitude<T> operator-() const
    {
        return longitude<T>{-this->value};
    }
};

template <typename T>
struct latitude : geographic_coordinate<T>
{
    using geographic_coordinate<T>::geographic_coordinate;
    constexpr latitude<T> operator-() const
    {
        return latitude<T>{-this->value};
    }
};

```


Now we have everything in place to start playing with our mini framework for geographical points:

```c++


int main()
{
    constexpr auto lon = -2_lon;
    constexpr auto lat = 51.8_lat;

    {
        constexpr geographic_point<double> p(lon, lat);
        std::cerr << p << std::endl;
    }
    {
        constexpr auto p = geographic_point<double>(lat, lon);
        std::cerr << p << std::endl;
    }
    {
        //constexpr geographic_point<double> p(52.0, -2.0);     // compile error
        //constexpr geographic_point<double> p(52_lon, -2_lon); // compile error
        //constexpr geographic_point<double> p(52_lat, -2_lat); // compile error
        //constexpr geographic_point<double> p(52_lat, -2); // compile error
        //constexpr geographic_point<double> p(52, -2_lon); // compile error

        constexpr geographic_point<double> p0(LON(30.0), -2.0_lat);  // OK
        constexpr geographic_point<double> p1(LON(30.0), LAT(-2.0)); // OK
        constexpr geographic_point<double> p2(LAT(-2.0), LON(30.0)); // OK
        constexpr geographic_point<double> p3(-2.0_lat, 30.4_lon);   // OK
        std::cerr << p0 << std::endl;
        std::cerr << p1 << std::endl;
        std::cerr << p2 << std::endl;
        std::cerr << p3 << std::endl;

        static_assert(p0 == p1, "FAIL");
        static_assert(p0 == p2, "FAIL");
        static_assert(p0 != p3, "FAIL");
    }
}
```


## Motivating example

The example above, as its title suggests, is rather contrived. Here is a more practical, IMHO, use-case for UDLs. In Mapnik, in order to deal with dispatching on string tags (retuned by parser) we use following recursive function to calculate `hash` value at compile time: https://github.com/mapnik/mapnik/blob/master/include/mapnik/util/name_to_int.hpp

This technique came about after having to deal with endless `if/else if/else` spaghetti statements in various input parsers.

```c++

if (name == "stroke")
{
   //
}
else if (name == "stroke-width")
{
   //
}
else if (name == "fill")
{
    ///
}
// add many more!!!

```

The code above is not efficient and is not-concise either. Specially for the large number of cases.  We can do better by introducing compile time `name_to_int` function and using `switch` statement instead :

```c++

constexpr unsigned name_to_int(char const* str, unsigned off = 0)
{
    return !str[off] ? 5381 : (name_to_int(str, off + 1) * 33) ^ static_cast<unsigned>(str[off]);
}


.....


auto val = name_to_int(name);
switch(val)
{
case name_to_int("stroke"):
    break;
case name_to_int("stroke-width"):
    break;
case name_to_int("fill"):
    break
    ...
    ...
default:
    break;
}

```

The `name_to_int` function feels like a great candidate for UDL. It might make code even more expressive. Let's give it a go:

```c++

constexpr unsigned operator"" _hash(char const* str, std::size_t)
{
    return name_to_int(str);
}

```


And now we can write:

```c++


auto val = name_to_int(name);
switch(val)
{
case "stroke"_hash:
    break;
case "stroke-width"_hash:
    break;
case "fill"_hash:
    break
    ...
    ...
default:
    break;
}

```

I hope you're convinced by now that UDLs can be a useful technique when coding in c++!
