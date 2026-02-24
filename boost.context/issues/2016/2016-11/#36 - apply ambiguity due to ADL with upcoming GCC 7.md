# #36 - apply ambiguity due to ADL with upcoming GCC 7 [Closed]

> Username: other-mickk  
> Created at: 2016-11-02 10:49:47 UTC  
> Updated at: 2016-12-11 14:07:00 UTC  
> Closed at: 2016-12-11 14:07:00 UTC  
> Url: https://github.com/boostorg/context/issues/36  

It looks like Boost.Context internally implements an [`apply`](https://github.com/boostorg/context/blob/88ce079b46934b56449299f80a1b3e9f195cd966/include/boost/context/detail/apply.hpp#L52-L62) facility to invoke a functor on tuple elements.  
  
GCC trunk has recently implemented the `std::apply` C++1z feature ([cppreference](http://en.cppreference.com/w/cpp/utility/apply)), slated for the GCC 7 release.  
  
This means that unqualified calls inside Boost.Context ([example](https://github.com/boostorg/context/blob/88ce079b46934b56449299f80a1b3e9f195cd966/include/boost/context/execution_context_v2.hpp#L93-L97)) easily end up being ambiguous. Both `apply` are found during overload resolution, `std::apply` being found via ADL e.g. as long as an `std::tuple` specialization is involved. Because they have near identical signatures, none is preferred over the other.

---

## Comment 1

> Username: olk  
> Created at: 2016-11-02 10:56:37 UTC  
> Url: https://github.com/boostorg/context/issues/36#issuecomment-257833010  

requires CXX17 defect macro in boost.config

---

## Comment 2

> Username: Flast  
> Created at: 2016-11-16 06:55:19 UTC  
> Url: https://github.com/boostorg/context/issues/36#issuecomment-260868788  

IMO, no config macro is necessary. Just qualify namespace `detail::` to 4 lines, [here](https://github.com/boostorg/context/blob/2e5430fd27b963ffd72e7ee820ae81bb34c17a33/include/boost/context/execution_context_v2.hpp#L93), [also here](https://github.com/boostorg/context/blob/db244547048aac37449bcd1d3f5dd24c73cc040b/include/boost/context/execution_context_v2_void.ipp#L16), [likewise](https://github.com/boostorg/context/blob/db244547048aac37449bcd1d3f5dd24c73cc040b/include/boost/context/execution_context_v2_void.ipp#L58) and [here](https://github.com/boostorg/context/blob/db244547048aac37449bcd1d3f5dd24c73cc040b/include/boost/context/execution_context_v1.hpp#L160).

---

## Comment 3

> Username: olk  
> Created at: 2016-12-11 14:07:00 UTC  
> Url: https://github.com/boostorg/context/issues/36#issuecomment-266284112  

fixed in e428581e1dfe037eb309e55ff14691f5c0d3c626
