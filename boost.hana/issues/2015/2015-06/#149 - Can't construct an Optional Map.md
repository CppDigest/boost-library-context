# #149 - Can't construct an Optional Map [Closed]

> Username: brunocodutra  
> Created at: 2015-06-21 21:47:16 UTC  
> Updated at: 2015-06-22 19:34:44 UTC  
> Closed at: 2015-06-22 19:34:44 UTC  
> Url: https://github.com/boostorg/hana/issues/149  

Calling just(make_map()) fails on Clang 3.6.1, whilst just(make_tuple()) compiles without an issue.  
Minimum code to reproduce:  
  
``` c++  
auto opt = boost::hana::just(boost::hana::make_map());  
```
