# #516 - logged_adaptor doesn't work [Closed]

> Username: jzmaddock  
> Created at: 2023-01-09 18:28:15 UTC  
> Updated at: 2023-01-14 13:14:01 UTC  
> Closed at: 2023-01-14 13:14:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/516  

The overloaded functions are never called, unless they are declared before any multiprecision includes, which basically sucks.  Much more investigation required.
