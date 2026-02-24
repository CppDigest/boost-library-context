# #581 - Refactor message and header constructors [Closed]

> Username: vinniefalco  
> Created at: 2017-07-03 23:42:43 UTC  
> Updated at: 2017-07-06 16:20:50 UTC  
> Closed at: 2017-07-06 16:20:50 UTC  
> Url: https://github.com/boostorg/beast/issues/581  

We'd like to be able to construct `request` and `response` objects with all the necessary fields like method, target, and result. The problem is that the advanced construction cases have already reserved the argument list so we would need to resort to a tag.  
  
Feedback suggests the constructors should make the simple cases easy and the advanced cases more verbose.  
  
One idea: normal constructors are for convenience, if you want to init the body you have to use `std::piecewise_construct, t` where `t` is the body argument. For body and fields you have to use `std::piecewise_construct, t, u` where `u` is the fields argument. And for body and fields with more than 1 argument you pass `std::tuple` there, like `std::piecewise_construct, std::make_tuple(...)` or `std::piecewise_construct, std::make_tuple(...), std::make_tuple(...)`  
  
This has to be worked out.
