# #217 - Propose for inplace construct from Fusion Sequence [Open]

> Username: correaa  
> Created at: 2019-12-05 23:54:15 UTC  
> Updated at: 2019-12-07 06:14:48 UTC  
> Url: https://github.com/boostorg/fusion/issues/217  

While defining semantic actions in Spirit X3, if find myself doing this a lot:  
  
```c++  
[](auto&& ctx){_val(ctx)=fusion::invoke([](auto const&... x){return MyType<U>{x...};}, _attr(ctx));}  
```  
  
That is to "invoke" a function just to construct an object of a type (which might or might not be the final representation assigned.)  
  
Of course, I could fusion adapt MyType, but sometimes this is an overkill.  
  
I think this can be condensed by a simple `construct` function in fusion.  
For example implemented like this:  
  
```c++  
template<class T, class Seq>  
auto construct(Seq&& seq){  
	return invoke([](auto const&... x){return T{x...};}, seq);  
}  
```  
  
This way the semantic action can be reduced to:  
  
```c++  
[](auto&& ctx){_val(ctx)=fusion::construct<MyType<U>>(_attr(ctx));}  
```  
  
The real power would be to use it in conjunction with CTAD (in c++17):  
  
```c++  
template<template<class...> class T, class Seq>  
auto construct(Seq&& seq){  
	return invoke([](auto const&... x){return T{x...};}, seq);  
}  
```  
  
```c++  
[](auto&& ctx){_val(ctx)=fusion::construct<MyType>(_attr(ctx));}  
```  
  
(`fusion::construct` could be called `fusion::make` alternatively).  
  
Does something like this exists in Fusion already?  
  
Would this be a useful addition to Boost.Fusion or is it too specific?
