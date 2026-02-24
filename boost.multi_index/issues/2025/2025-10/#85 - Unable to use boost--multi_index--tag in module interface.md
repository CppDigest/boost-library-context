# #85 - Unable to use boost::multi_index::tag in module interface [Closed]

> Username: d7d1cd  
> Created at: 2025-10-14 13:36:40 UTC  
> Updated at: 2025-10-23 10:16:14 UTC  
> Closed at: 2025-10-23 10:16:14 UTC  
> Url: https://github.com/boostorg/multi_index/issues/85  

When trying to use boost::multi_index::tag in the implementation of the interface part of a C++20 module, I get a GCC error that the boost part uses internal linkage. Here's a reproducible example: https://godbolt.org/z/5e7KWj8qz

---

## Comment 1

> Username: d7d1cd  
> Created at: 2025-10-14 16:05:53 UTC  
> Updated at: 2025-10-14 16:08:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/85#issuecomment-3407422740  

The problem is that the [boost::mpl::aux::ptr_to_ref](https://github.com/boostorg/mpl/blob/develop/include/boost/mpl/aux_/ptr_to_ref.hpp#L42) function template is declared as static. Is there a reason for this, or is it just a bug?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-10-15 16:43:53 UTC  
> Url: https://github.com/boostorg/multi_index/issues/85#issuecomment-3407422755  

Someone is still using MPL ?

---

## Comment 3

> Username: d7d1cd  
> Created at: 2025-10-16 05:26:55 UTC  
> Url: https://github.com/boostorg/multi_index/issues/85#issuecomment-3409229017  

> Someone is still using MPL ?  
  
Boost itself uses MPL in its implementation of multi_index::tag

---

## Comment 4

> Username: d7d1cd  
> Created at: 2025-10-16 05:27:55 UTC  
> Url: https://github.com/boostorg/multi_index/issues/85#issuecomment-3409230679  

To solve the problem, I made a PR https://github.com/boostorg/mpl/pull/90
