# #32 - Mismatch of #pragma warning push/pop [Open]

> Username: Trigve  
> Created at: 2021-06-03 18:53:53 UTC  
> Updated at: 2021-06-03 18:53:53 UTC  
> Url: https://github.com/boostorg/proto/issues/32  

Using VS 2019 (16.10.0)  there is mismatch of #pragma warning push/pop. The problem is in the given lines (and also 229):  
https://github.com/boostorg/proto/blob/7f924934689b940f3a72212ab0f714ec6fd6e34b/include/boost/proto/operators.hpp#L209-L212  
  
I've modified it like below and now it is working ok:  
```cpp  
BOOST_PROTO_PUSH_WARNINGS                                                             \  
BOOST_PROTO_DISABLE_MSVC_C4714                                                        \  
template<typename Arg>                                                                \  
BOOST_FORCEINLINE                                                                     \  
typename boost::proto::detail::enable_unary<                                          \  
```
