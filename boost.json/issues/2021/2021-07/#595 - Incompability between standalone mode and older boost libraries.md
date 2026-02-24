# #595 - Incompability between standalone mode and older boost libraries [Closed]

> Username: doganulus  
> Created at: 2021-07-23 14:15:59 UTC  
> Updated at: 2022-11-16 12:32:06 UTC  
> Closed at: 2022-11-16 12:32:05 UTC  
> Url: https://github.com/boostorg/json/issues/595  

I would like to report an edge case that there is some dependency left between standalone Boost::JSON library and older Boost libraries. Below is a minimal example failing to compile with `BOOST_STATIC_ASSERT` errors inside standard `manylinux2014` docker environment, which is based on `Centos7` having `Boost v1.53` using `g++9.3`.   
  
```cpp  
#include <iostream>  
#include <boost/bimap/bimap.hpp> // From Boost 1.53. (The program compiles when removed.)  
  
#define BOOST_JSON_STANDALONE 1  
#include <boost/json.hpp> // From Boost::JSON 1.76.  
  
int main(){  
  
  auto ja = boost::json::array({1,2,3});  
  std::cout << ja << std::endl;  
  
  return 0;  
}  
```  
  
The compilation produces several error messages such as:  
  
```cpp  
/usr/local/include/boost/json/storage_ptr.hpp:491:39: error: macro "BOOST_STATIC_ASSERT" passed 2 arguments, but takes just 1  
  491 |             memory_resource, T>::value);  
      |                                       ^  
In file included from /usr/include/boost/serialization/tracking.hpp:20,  
                 from /usr/include/boost/serialization/nvp.hpp:32,  
                 from /usr/include/boost/bimap/bimap.hpp:57,  
                 from /workspaces/vevatools/data/bsa_example.cpp:4:  
/usr/include/boost/static_assert.hpp:56: note: macro "BOOST_STATIC_ASSERT" defined here  
   56 | #  define BOOST_STATIC_ASSERT( B ) static_assert(B, #B)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-23 15:28:31 UTC  
> Updated at: 2021-07-23 15:28:52 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885719270  

You may be surprised to discover that I did not test Boost.JSON with every possible earlier version of Boost :) I'm not sure that this is a supported configuration. @glenfe ?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-07-23 16:25:23 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885754330  

You need to enclose the expression in parentheses.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-07-23 16:26:07 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885754763  

Although... why does Boost.JSON use BOOST_STATIC_ASSERT in standalone mode?

---

## Comment 4

> Username: glenfe  
> Created at: 2021-07-23 23:40:46 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885963281  

It defines BOOST_STATIC_ASSERT if it doesn't exist, but it would better if it did not do this:  
https://github.com/boostorg/json/blob/master/include/boost/json/detail/config.hpp#L93-L95  
  
Instead it could unconditionally use `BOOST_JSON_STATIC_ASSERT`  which in non-standalone mode might make use of `BOOST_STATIC_ASSERT`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-07-24 00:00:07 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885967042  

Ah this is with standalone Boost.JSON, which I suppose in theory should work with any other library. There are a few other boost macros that it defines in standalone.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-07-24 01:36:32 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-885982807  

Same with all the other Boost macros - Boost.JSON shouldn't (re)define them.

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-11-16 12:32:05 UTC  
> Url: https://github.com/boostorg/json/issues/595#issuecomment-1316947056  

This was "solved" by removing the standalone mode.
