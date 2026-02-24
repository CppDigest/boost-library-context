# #14 CMake support [Closed]

> Username: HDembinski  
> Created at: 2019-06-02 19:02:29 UTC  
> Updated at: 2019-06-03 18:00:07 UTC  
> Closed at: 2019-06-03 18:00:02 UTC  
> Url: https://github.com/boostorg/variant2/pull/14  

To use variant2 in Boost.Histogram, CMake support is needed.  
  
I copied the CMakeLists.txt from Mp11 and adapted it. variant2 itself has very few dependencies, but the tests add a lot of additional dependencies. When I boost_fetch variant2 from Boost.Histogram, variant2 also boost_fetch-es the dependencies for the tests, which are not needed then. I thought that the line `if (CMAKE_SOURCE_DIR STREQAL CMAKE_CURRENT_SOURCE_DIR)` was supposed to prevent this, but it doesn't. This also needs to be fixed in Mp11.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-06-02 20:27:17 UTC  
> Url: https://github.com/boostorg/variant2/pull/14#issuecomment-498062736  

>  I thought that the line `if (CMAKE_SOURCE_DIR STREQAL CMAKE_CURRENT_SOURCE_DIR)` was supposed to prevent this, but it doesn't.  
  
It might have prevented it, had it been present. But you check `PROJECT_SOURCE_DIR` instead for some reason.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-06-03 10:23:57 UTC  
> Url: https://github.com/boostorg/variant2/pull/14#issuecomment-498201219  

I tried both, neither works in the intended way?

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-06-03 18:00:02 UTC  
> Url: https://github.com/boostorg/variant2/pull/14#issuecomment-498362277  

I am going to close this, since develop works for me.

---
