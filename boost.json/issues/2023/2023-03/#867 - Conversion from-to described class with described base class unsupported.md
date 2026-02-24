# #867 - Conversion from/to described class with described base class unsupported [Closed]

> Username: sdebionne  
> Created at: 2023-03-06 16:49:36 UTC  
> Updated at: 2023-03-08 10:54:05 UTC  
> Closed at: 2023-03-08 10:54:05 UTC  
> Url: https://github.com/boostorg/json/issues/867  

### Version of Boost  
  
Boost 1.81  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <boost/describe.hpp>  
#include <boost/json/conversion.hpp>  
  
namespace test  
{  
  
struct base {  
    char m0;  
};  
  
BOOST_DESCRIBE_STRUCT(base, (), (m0))  
  
struct bar : base {  
   int m1;  
   float m2;  
};  
  
BOOST_DESCRIBE_STRUCT(bar, (base), (m1, m2))  
  
} //namespace test  
  
int main(){  
    static_assert(boost::json::is_described_class<test::bar>::value);       // KO  
    static_assert(boost::describe::has_describe_members<test::bar>::value); // OK  
}  
```  
  
I wonder what is the rational behind filtering the classes with `described_bases` in:  
  
```cpp  
template<class T>  
struct is_described_class  
    : mp11::mp_and<  
        describe::has_describe_members<T>,  
        mp11::mp_not< std::is_union<T> >,  
        mp11::mp_empty<  
            mp11::mp_eval_or<  
                mp11::mp_list<>, detail::described_non_public_members, T>>,  
        mp11::mp_empty<  
            mp11::mp_eval_or<mp11::mp_list<>, detail::described_bases, T>>>  
{ };  
```  
  
especially if the bases have only public described members?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-03-06 17:20:51 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1456570266  

It's not clear what the JSON representation should be. Flattened? Or serialize the bases separately somehow?  
  
Most people tend to want flattened even though it wasn't something I considered initially.

---

## Comment 2

> Username: sdebionne  
> Created at: 2023-03-06 17:29:47 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1456588031  

I see. I would expect flattened too.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-03-06 17:40:36 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1456612487  

That's easy to implement (mod_inherited) as long as we agree we want it.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-03-06 18:48:28 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1456756494  

Yeah. With generic conversions we try to be noncontroversial.

---

## Comment 5

> Username: sdebionne  
> Created at: 2023-03-06 21:10:39 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457007683  

> With generic conversions we try to be noncontroversial.  
  
Do you mean you want to support both options?  
  
Actually, I had a similar implementation in my code to support generic conversion from/to described class but with a different policy (flatten representation of class hierarchy) . After updating to 1.81, given that the implementation of conversion is monolithic, how am I supposed to overload it with a new "policy" (for derived class)?

---

## Comment 6

> Username: sdebionne  
> Created at: 2023-03-06 21:16:15 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457014457  

I agree that the library should probably not make a decision and leave that to the user. I am just not sure if it's possible with the current implementation. There cannot be a another user customization for a conversion that is already implemented in the library, no?

---

## Comment 7

> Username: pdimov  
> Created at: 2023-03-06 23:25:09 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457205026  

A tag_invoke for your class has precedence. Or, you can specialize is_described_class<T> to false, but then it just wouldn't work at all.

---

## Comment 8

> Username: sdebionne  
> Created at: 2023-03-07 06:52:11 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457636465  

Indeed, it seams that I did not read the manual in detail, sorry for that. Specializing `is_described_class` to`std::true_type` does what I want, as documented.  
  
```cpp  
#include <iostream>  
  
#include <boost/describe.hpp>  
#include <boost/json/src.hpp>  
  
namespace test  
{  
  
struct base {  
    char m0;  
};  
  
BOOST_DESCRIBE_STRUCT(base, (), (m0))  
  
struct bar : base {  
   int m1;  
   float m2;  
};  
  
BOOST_DESCRIBE_STRUCT(bar, (base), (m1, m2))  
  
} //namespace test  
  
namespace boost::json {  
    template <>  
    struct is_described_class<test::bar> : std::true_type {};  
}  
  
int main(){  
  
    // Print {"m0":0,"m1":0,"m2":0E0}  
    std::cout << boost::json::value_from(test::bar()) << std::endl;  
  
    return 0;  
}  
```  
  
But I wish there was a way to set this project wide, not for every single classes...

---

## Comment 9

> Username: sdebionne  
> Created at: 2023-03-07 07:58:11 UTC  
> Updated at: 2023-03-07 08:02:23 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457714602  

Do you see a better solution than resort to using a macro like:  
  
```cpp  
#define BOOST_JSON_FLATTENED_CLASS(Described)                               \  
    namespace boost::json {                                         \  
    template <typename T> struct is_described_class;                \  
    template <> struct is_described_class<Described> : std::true_type {};   \  
    }  
```  
  
for every classes that use inheritance?

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-03-07 10:52:17 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1457957606  

As Peter wrote, you can provide your own `tag_invoke` overloads for described classes and they will take precedence. Your overloads can use `enable_if` to match all described classes. I'm not sure if this is any better than the macro.

---

## Comment 11

> Username: sdebionne  
> Created at: 2023-03-08 10:54:05 UTC  
> Url: https://github.com/boostorg/json/issues/867#issuecomment-1459981050  

Providing my own `tag_invoke` generic overload for described classes indeed works, I had the feeling that doing so would introduce some ambiguite (overlapping the built-in conversions). This is of course much better than a macro!
