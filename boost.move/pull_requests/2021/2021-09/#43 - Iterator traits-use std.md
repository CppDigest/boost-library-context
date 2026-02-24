# #43 Iterator traits/use std [Closed]

> Username: theodelrieu  
> Created at: 2021-09-20 16:05:54 UTC  
> Updated at: 2022-07-10 16:59:25 UTC  
> Closed at: 2022-07-10 16:59:25 UTC  
> Url: https://github.com/boostorg/move/pull/43  

Using Boost.Move breaks range-v3, since the latter specializes std::iterator_traits for its custom iterators, but does not provide all member type aliases.  
  
See https://github.com/ericniebler/range-v3/pull/1660

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-12-26 16:48:48 UTC  
> Url: https://github.com/boostorg/move/pull/43#issuecomment-1001211585  

I could not reproduce the issue with the following test with latest MSVC 2019 andd GCC 10.3 in C++17/20 modes:  
  
```  
#include <range/v3/all.hpp>  
#include <boost/container/flat_map.hpp>  
  
int main()  
{  
   using namespace boost::container;  
   auto f = ranges::to< flat_map<int, int> >();  
   (void)f;  
   return 0;  
}`  
```  
  
I'm using Boost 1.78 and Ranges-v3 0.11 and current master. Could you provide a minimal test case and compiler/toolset versions to reproduce the bug?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2022-07-10 16:59:25 UTC  
> Url: https://github.com/boostorg/move/pull/43#issuecomment-1179763296  

Closing the issue due to inactivity.

---
