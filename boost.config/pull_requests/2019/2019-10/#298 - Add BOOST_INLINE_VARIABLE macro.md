# #298 Add BOOST_INLINE_VARIABLE macro [Merged]

> Username: Lastique  
> Created at: 2019-10-02 14:22:06 UTC  
> Updated at: 2019-10-29 17:35:31 UTC  
> Merged at: 2019-10-29 16:56:21 UTC  
> Closed at: 2019-10-29 16:56:21 UTC  
> Url: https://github.com/boostorg/config/pull/298  

The `BOOST_INLINE_VARIABLE` macro can be used to mark C++17 inline variables in contexts where omitting the `inline` keyword is still acceptable on older compilers.  
  
A few use cases:  
  
https://github.com/boostorg/atomic/blob/fc410975146a23e0513e8164009cc1eac2d9830b/include/boost/memory_order.hpp#L57  
https://github.com/boostorg/core/issues/63  
https://github.com/boostorg/optional/issues/33  
  
This commit also adds the missing docs for `BOOST_NO_CXX17_INLINE_VARIABLES`.

---
