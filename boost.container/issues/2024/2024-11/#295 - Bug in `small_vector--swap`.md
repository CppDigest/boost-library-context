# #295 - [1.86] Bug in `small_vector::swap` [Closed]

> Username: Morddin  
> Created at: 2024-11-20 16:57:26 UTC  
> Updated at: 2024-11-21 14:49:30 UTC  
> Closed at: 2024-11-21 14:49:13 UTC  
> Url: https://github.com/boostorg/container/issues/295  

In the following code, the assertion fails:  
```cpp  
#include <boost/container/small_vector.hpp>  
#include <cassert>  
  
int main()  
{  
    auto v1 = boost::container::small_vector<int, 4>{0, 1, 2, 3, 4};  
    auto v2 = boost::container::small_vector<int, 4>{5};  
    v1.swap(v2);  
    assert(v1[0] == 5);  
}  
```  
  
A quick look makes me think that `this->priv_raw_begin()` should be replaced with `intmem.priv_raw_begin()` in the following two places:  
  
https://github.com/boostorg/container/blob/83b8d5748dd93617bec92b1b31800213167a5cb9/include/boost/container/vector.hpp#L2767-L2768  
https://github.com/boostorg/container/blob/83b8d5748dd93617bec92b1b31800213167a5cb9/include/boost/container/vector.hpp#L2778-L2780

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-11-21 14:49:28 UTC  
> Url: https://github.com/boostorg/container/issues/295#issuecomment-2491434456  

Many thanks for the report and the bug analysis!
