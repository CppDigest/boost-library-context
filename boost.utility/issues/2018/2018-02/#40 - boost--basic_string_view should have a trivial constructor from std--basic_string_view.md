# #40 - boost::basic_string_view should have a trivial constructor from std::basic_string_view [Open]

> Username: cedral  
> Created at: 2018-02-09 19:44:14 UTC  
> Updated at: 2022-08-30 22:13:01 UTC  
> Url: https://github.com/boostorg/utility/issues/40  

I am trying to use beast which makes heavy use of boost::string_view in code that already makes heavy use of std::string_view. It is awfully easy to make a constexp constructor that trivially converts a std::string_view to a boost::string_view. I added one myself but I would appreciate it if you guys would go ahead and add it in too.   
  
```  
      BOOST_CONSTEXPR basic_string_view(const std::basic_string_view<charT, traits>& str) BOOST_NOEXCEPT  
      : ptr_(str.data()), len_(str.size()) {}  
```

---

## Comment 1

> Username: mclow  
> Created at: 2018-02-09 20:03:29 UTC  
> Updated at: 2018-02-09 20:04:15 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-364548302  

This has come up before, and would require a dependency between `boost::string_view` and `std::string_view` (which implies C++17 only).  
  
Better for you (who already have both included) to define a conversion function. Something like:  
  
    template <typename charT, typename Traits>  
    BOOST_CONSTEXPR operator boost::basic_string_view<charT, Traits>   
        (const std::basic_string_view<charT, Traits>& str) BOOST_NOEXCEPT  
        { return boost::basic_string_view<charT, Traits>(str.data()), str.size()); }

---

## Comment 2

> Username: markazmierczak  
> Created at: 2018-03-15 09:55:02 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-373320892  

@mclow your example will not compile. C++ does not support out of class operators - they must me members of the class:  
http://en.cppreference.com/w/cpp/language/cast_operator  
In my opinion it would be much better for Boost to just alias std::basic_string_view as boost::basic_string_view if it's available.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-03-15 10:06:23 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-373324071  

`boost::basic_string_view` is not equivalent to `std::basic_string_view`.

---

## Comment 4

> Username: dimztimz  
> Created at: 2018-04-22 14:34:10 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-383385838  

I was thinking, if c++17 is enabled, `boost::string_view` may inherit from `std::string_view` and implement the additional functions in terms of the public interface (data, size, traits_type, etc). The inherited class won't add any new data members, so slicing would be same as copy.  
  
It can be done with macros or with template trickery.  
  
```c++  
#if __cplusplus >= 2017...  
class basic_string_view: public std::basic_string_view {  
//...  
#else  
class basic_string_view {  
//...  
#endif  
```

---

## Comment 5

> Username: mclow  
> Created at: 2018-04-23 15:56:28 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-383626958  

I'd rather not require a dependency on `std::string_view` in `boost::string_view`.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-12-18 22:55:19 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-448402679  

Why is the dependency a problem (when C++17 and `__has_include(<string_view>)`)?

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-06-15 14:12:13 UTC  
> Updated at: 2020-06-15 14:12:43 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-644159553  

This issue is relevant to me in the last 4 applications I have written.   
C++17 and 20 are now the standard of choice when writing applications and servers. std::string_view is ubiquitous, and I use many boost libraries which use boost's version (beast, boost.json etc).  
  
Annoyingly, boost.asio uses std::string_view. Regular conversions are inevitable. This adds noise to the source code at the call sites of functions involving string_view - the exact opposite of its intent.  
  
It means that every project needs a file like this:  
  
```  
#pragma once  
#include <boost/json.hpp>  
#include <string_view>  
  
namespace my_awesome_project::config::json  
{  
    using namespace boost::json;  
  
    inline auto convert(std::string_view sv)  
    {  
        return string_view(sv.data(), sv.size());  
    }  
  
    inline auto convert(json::string_view sv) { return sv; }  
}   // namespace my_awesome_project::config::json  
```  
  
The automatic conversion when c++17 or higher is detected would be most welcome.

---

## Comment 8

> Username: anarthal  
> Created at: 2020-06-16 19:27:51 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-644967626  

Having a default constructor from std::basic_string_view would be really useful for the MySQL client library I am writing and want to propose for Boost.

---

## Comment 9

> Username: TheStormN  
> Created at: 2021-06-14 15:05:38 UTC  
> Updated at: 2021-06-14 18:51:24 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-860759410  

+1 for this one. There are good compile time checks about C++ version so `std::string_view` can be a dependency only in C++17 mode.  
  
@mclow please reconsider

---

## Comment 10

> Username: kamrann  
> Created at: 2022-08-18 16:06:57 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-1219674898  

> I'd rather not require a dependency on `std::string_view` in `boost::string_view`.  
  
This seems rather insignificant given that you already have a dependency on `<string>`.  
  
That aside, perhaps this issue could be solved by adding a range-based constructor, just as `std::string_view` gets in `c++23`?

---

## Comment 11

> Username: Lastique  
> Created at: 2022-08-30 20:59:41 UTC  
> Updated at: 2022-08-30 22:13:01 UTC  
> Url: https://github.com/boostorg/utility/issues/40#issuecomment-1232162172  

> That aside, perhaps this issue could be solved by adding a range-based constructor, just as `std::string_view` gets in `c++23`?  
  
That constructor is causing problems in real code bases and IMO is a mistake. See [P2516](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2516r0.html) for example. It is causing problems in Boost.Filesystem as well, as I have to special-case `std::string_view` to avoid overload ambiguities in `path` implementation. I'd be opposed to adding it to `boost::string_view`.  
  
Actually, I was told that [P2499](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p2499r0.html) was merged into the standard draft, making the constructor `explicit`. So this constructor won't cause as much trouble but also won't help with conversion from `std::string_view` to `boost::string_view`.
