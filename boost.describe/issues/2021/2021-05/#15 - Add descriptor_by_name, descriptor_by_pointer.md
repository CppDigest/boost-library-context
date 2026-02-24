# #15 - Add descriptor_by_name, descriptor_by_pointer [Closed]

> Username: pdimov  
> Created at: 2021-05-07 01:36:59 UTC  
> Updated at: 2021-10-27 03:40:22 UTC  
> Closed at: 2021-10-27 03:40:21 UTC  
> Url: https://github.com/boostorg/describe/issues/15  

This can be useful for "detached" annotations, as suggested by @grisumbras:  
```  
namepace mine {  
template <class T> struct annotation {};  
} //mine  
  
struct foobar {  
  int foo;  
  double bar;  
};  
BOOST_DESCRIBE_STRUCT(foobar, (), (foo, bar))  
  
namepace mine {  
template <> struct annotation<BOOST_DESCRIBE_GET_MEMBER(foobar, foo)> { constexpr auto& value = "some text here"; }  
} // namespace mine  
```  
In C++14, a `descriptor_by_name` can be implemented and used like this:  
```  
using L1 = boost::describe::describe_members<X, boost::describe::mod_any_access>;  
using N1 = BOOST_DESCRIBE_MAKE_NAME( y );  
using D1 = descriptor_by_name<L1, N1>;  
```  
whereas in C++17 a `descriptor_by_pointer` can be used like this:  
```  
using D2 = descriptor_by_pointer<L1, &X::y>;  
```

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-06-08 13:40:56 UTC  
> Url: https://github.com/boostorg/describe/issues/15#issuecomment-856777942  

Just to show my interest in this feature, here is a (naive) implementation of `descriptor_by_name` with C++20, that requires an implementation of a compile time string such as [P0259](http://open-std.org/JTC1/SC22/WG21/docs/papers/2016/p0259r0.pdf).  
  
```cpp  
#include <iostream>  
  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
  
#include "fixed_string.hpp"  
  
using namespace fixstr;  
  
struct foo  
{  
    int length;  
    int resistance;  
};  
  
BOOST_DESCRIBE_STRUCT(foo, (), (length, resistance))  
  
namespace boost {  
    namespace describe {  
  
        template <fixed_string name>  
        struct by_name {  
            template <typename D>  
            struct fn {  
                static constexpr bool value = (name == std::string_view(D::name));  
            };  
        };  
  
        template <  
            typename T,  
            fixed_string name,  
            typename D = describe_members<T, mod_any_access >,  
            typename P = by_name<name>  
        >  
        using descriptor_by_name = mp11::mp_at<D, mp11::mp_find_if_q<D, P>>;  
  
    }  
}  
  
using namespace boost::describe;  
  
// Annotations  
template <typename T> struct annotation;  
template <> struct annotation<descriptor_by_name<foo, "length">> {  static constexpr auto& unit = "cm"; };  
template <> struct annotation<descriptor_by_name<foo, "resistance" >> { static constexpr auto& unit = "ohm"; };  
  
template <typename T>  
void print_with_annotation(T const& x)  
{  
    using Md = describe_members<T, mod_any_access>;  
  
    bool first = true;  
    boost::mp11::mp_for_each<Md>([&](auto D) {  
  
        if (!first) { std::cout << ", " << std::endl; } first = false;  
  
        std::cout << "." << D.name << " = " << x.*D.pointer << annotation<decltype(D)>::unit;   
    });  
}  
  
int main()  
{  
    foo bar{ 1, 2 };  
  
    print_with_annotation(bar);  
  
    return 0;  
}  
```  
  
I was hoping that the type of the member descriptor could be generated directly, that is without looking up the descriptor list, but that does not sound to be possible with the current approach.

---

## Comment 2

> Username: sdebionne  
> Created at: 2021-06-15 08:32:29 UTC  
> Url: https://github.com/boostorg/describe/issues/15#issuecomment-861301040  

Here is an implementation of `descriptor_by_pointer`:  
  
```cpp  
namespace boost::describe  
{  
    namespace detail  
    {  
        template <auto pointer>  
        struct by_pointer  
        {  
            template <typename D, typename T1, typename T2>  
            struct equal { static constexpr bool value = false; };  
  
            template <typename D, typename T>  
            struct equal<D, T, T> { static constexpr bool value = (pointer == D::pointer); };  
  
            template <typename D>  
            struct fn  
            {  
                static constexpr bool value =  
                    equal<D, std::decay_t<decltype(pointer)>, std::decay_t<decltype(D::pointer)>>::value;  
            };  
        };  
    } //namespace detail  
  
    template <typename Md, auto pointer, typename P = detail::by_pointer<pointer>>  
    using descriptor_by_pointer = mp11::mp_at<Md, mp11::mp_find_if_q<Md, P>>;  
  
} //namespace boost::describe  
```  
  
I have put together a [gist that shows a possible way to implement annotations](https://gist.github.com/sdebionne/af4e442eebef11638f2808b930b8508f) with `descriptor_by_pointer` (the motivating idea behind this issue) on struct members and print a data structure recursively.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-10-27 03:40:21 UTC  
> Url: https://github.com/boostorg/describe/issues/15#issuecomment-952509656  

Implemented.
