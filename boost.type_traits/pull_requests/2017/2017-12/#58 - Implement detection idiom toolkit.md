# #58 Implement detection idiom toolkit [Merged]

> Username: glenfe  
> Created at: 2017-12-04 13:37:26 UTC  
> Updated at: 2018-02-07 00:38:23 UTC  
> Merged at: 2018-01-31 18:55:39 UTC  
> Closed at: 2018-01-31 18:55:40 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58  

Implements the Detection Idiom [N4502](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4502.pdf) adopted into <experimental/type_traits> into C++ standard Fundamentals TS v2.  
  
Example usage: My `std::to_address` that was adopted for C++20 ([P0653R2](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0653r2.html)) could be implemented conveniently as:  
```  
template<class T>  
using __to_address_t = decltype(pointer_traits<T>::to_address(declval<const T&>()));  
  
template<class T>  
constexpr T* to_address(T* p) noexcept  
{  
    return p;  
}  
  
template<class T>  
auto to_address(const T& p) noexcept  
{  
    if constexpr (is_detected_v<__to_address_t, T>) {  
        return pointer_traits<T>::to_address(p);  
    }  else {  
        return std::to_address(p.operator->());  
    }  
}  
```

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-12-05 01:10:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-349161562  

John, do you want each trait here to have its own QBK documentation page, or can I consolidate them all in one?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-01-25 10:42:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-360429310  

Apologies for just catching up with this - at present I'm not really clear what these traits actually do - but normally, yes one page per trait.  In fact normally one header per trait as well...

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-01-26 06:07:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-360692879  

Updated the pull request: One header per trait, and one documentation page per trait.  
  
> at present I'm not really clear what these traits actually do  
  
I also updated the original comment of this pull request with a summary and example.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-01-28 10:59:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-361054269  

I'm going to wait for the churn in the library to settle before merging this (ie clean CI).  
  
Can I get you to link to the original paper in the docs?  Also some small examples illustrating the difference between the various traits would be very helpful.  
  
Bloody clever idiom BTW :)

---

## Comment 5

> Username: glenfe  
> Created_at: 2018-01-28 18:20:38 UTC  
> Updated_at: 2018-01-29 19:31:10 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-361083697  

> Can I get you to link to the original paper in the docs? Also some small examples illustrating the difference between the various traits would be very helpful.  
  
Done (link) and [somewhat] done (examples). :)

---

## Comment 6

> Username: glenfe  
> Created_at: 2018-01-30 18:26:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-361687873  

Travis and Appveyor confirmed Green (like Lantern).  Not Red (like Tornado).

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-02-05 18:11:46 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-363170828  

This is working very nicely with gcc, not so much with msvc, for example given:  
  
```  
template <class T>  
using has_equal_to_func = decltype(std::declval<T>() == std::declval<T>());  
  
template <class T>  
using has_equal_to = boost::is_detected<has_equal_to_func, T>;  
```  
  
and  
  
```  
struct tester{};  
  
static_assert(! ::has_equal_to<tester>::value);  
```  
  
Then msvc generates:  
  
```  
m:\data\boost\boost\t.cpp(15): error C2676: binary '==': 'tester' does not define this operator or a conversion to a type acceptable to the predefined operator  
```  
  
Annoyingly, msvc's intellisense does accept the code!  
  
I'm not sure if there's anything we can do to fix this?

---

## Comment 8

> Username: glenfe  
> Created_at: 2018-02-05 21:16:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-363223921  

Which version of MSVC? I'm guessing 2017 given the use of C++17 form of static_assert. Can you check how that version behaves with:  
```cpp  
#include <boost/type_traits/declval.hpp>  
#include <boost/type_traits/integral_constant.hpp>  
#include <boost/type_traits/make_void.hpp>  
  
template<class, class = void>  
struct has_equal_to  
    : boost::false_type { };  
  
template<class T>  
struct has_equal_to<T,  
    boost::void_t<decltype(boost::declval<T>() == boost::declval<T>())> >  
    : boost::true_type { };  
  
struct tester { };  
  
static_assert(!has_equal_to<tester>::value, "et cetera");  
```  
If it is the usual MSVC expression-SFINAE support deficiency, I suspect there is not much we can do.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2018-02-06 17:15:49 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-363495851  

Yep, so far as I can tell, direct use of the detection idiom works fine (including your code above), it's only when invoked via is_detected that it fails.

---

## Comment 10

> Username: glenfe  
> Created_at: 2018-02-06 19:57:01 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-363545768  

In that case there might be some opportunity for MSVC-specific workarounds in the detection idiom. I'll see what I can do, tonight.

---

## Comment 11

> Username: glenfe  
> Created_at: 2018-02-07 00:38:23 UTC  
> Url: https://github.com/boostorg/type_traits/pull/58#issuecomment-363615289  

Solution found: Pull request incoming.

---
