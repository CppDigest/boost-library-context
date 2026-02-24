# #39 greg_month.hpp: switched to std::shared_ptr for c++11 and newer [Closed]

> Username: tlanc007  
> Created at: 2017-05-19 03:42:18 UTC  
> Updated at: 2017-06-30 03:41:23 UTC  
> Closed at: 2017-06-30 03:04:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/39  

For older compilers or to use boost::shared_ptr use  
BOOST_NO_CXX11_SMART_PTR.  This change allows for compiling with C++17.  
With this change all return from year_month_date() to use structured  
binding

---

## Comment 1

> Username: mclow  
> Created_at: 2017-06-28 21:14:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-311793703  

I talked to Jeff, and he feels that this is a transparent, breaking change in the interface of Boost.Date time (on C++11/14/17 systems).  
  
While it's a good idea to allow people to use `std::shared_ptr` (and others) when they are available, I don't think unilaterally making that decision for them is a good idea.  Consider two separate source files, both using Boost.DateTime, one compiled as C++03, the other as C++11. They would have different types for `month_map_ptr_type`.

---

## Comment 2

> Username: tlanc007  
> Created_at: 2017-06-29 02:48:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-311847514  

> On Jun 28, 2017, at 3:14 PM, Marshall Clow <notifications@github.com> wrote:  
>   
> I talked to Jeff, and he feels that this is a transparent, breaking change in the interface of Boost.Date time (on C++11/14/17 systems).  
>   
> While it's a good idea to allow people to use std::shared_ptr (and others) when they are available, I don't think unilaterally making that decision for them is a good idea. Consider two separate source files, both using Boost.DateTime, one compiled as C++03, the other as C++11. They would have different types for month_map_ptr_type.  
>   
That is true.  
  
Then your suggestion would be roll back, use the boost smart pointers, and then use the NO_AUTO check so date_time will compile under C++17, right?  
  
Tal

---

## Comment 3

> Username: mclow  
> Created_at: 2017-06-29 15:00:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-311993989  

My suggestion is to not make this change at all - continue to use the boost smart pointers.  
If the boost smart pointers don't compile under C++17 (they *do* for libc++), then they should be fixed (not DateTime)

---

## Comment 4

> Username: mclow  
> Created_at: 2017-06-29 15:31:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312003392  

I wasn't clear; the correct place to make the change is in Boost.Config - to define `BOOST_NO_AUTO_PTR` when building for C++17 (if the standard library has deleted `auto_ptr`).

---

## Comment 5

> Username: mclow  
> Created_at: 2017-06-29 15:35:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312004807  

take a look at `boost/config/stdlib/libcpp.hpp` about line # 96:  
  
    #if (_LIBCPP_VERSION > 4000) && (__cplusplus > 201402L) && !defined(_LIBCPP_ENABLE_CXX17_REMOVED_AUTO_PTR)  
    #  define BOOST_NO_AUTO_PTR  
    #endif

---

## Comment 6

> Username: tlanc007  
> Created_at: 2017-06-30 03:04:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312162706  

Got it.    
  
Closing this out as **date_time** builds just fine under C++17.  And as Marshall pointed out, the new code produces different types depending on which version of C++ it is compiled with.

---

## Comment 7

> Username: mclow  
> Created_at: 2017-06-30 03:24:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312165047  

I'll talk to Jeff about this; I'm pretty sure that there's a good way to let people choose between `std:shared_ptr` vs. `boost::shared_ptr`, but it needs to be something that they can choose, not that we choose for them.

---

## Comment 8

> Username: mclow  
> Created_at: 2017-06-30 03:25:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312165101  

There are people who prefer the boost classes to the std ones. Don't want to upset them.

---

## Comment 9

> Username: eldiener  
> Created_at: 2017-06-30 03:41:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/39#issuecomment-312166730  

The cxxd library lets the end-user choose between std:shared_ptr vs. boost::shared_ptr easily enough.

---
