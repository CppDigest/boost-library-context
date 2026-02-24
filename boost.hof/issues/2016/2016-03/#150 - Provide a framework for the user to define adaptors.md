# #150 - Provide a framework for the user to define adaptors [Open]

> Username: pfultz2  
> Created at: 2016-03-15 20:15:13 UTC  
> Updated at: 2016-03-26 02:06:10 UTC  
> Url: https://github.com/boostorg/hof/issues/150  



---

## Comment 1

> Username: viboes  
> Created at: 2016-03-15 20:57:38 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-197016416  

This is not what I read as comment during the review. The reviewer requested to show how an adaptor is written.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-15 21:41:53 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-197035223  

Having a framework to build adaptors can simplify the building of adaptors in the library, and simplify how they could be defined by the user.

---

## Comment 3

> Username: viboes  
> Created at: 2016-03-15 22:43:23 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-197056607  

You are right, but people want to know how to define flat adaptors as the ones you have defined.  
  
having other functions that help to define them would be worth the effort, that could be justified because adding flat adaptors is maybe complex or cumbersome.  
  
So even if you opt for a framework, we would need to explain why do we need them.  
  
Well this is just my opinion.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-03-15 22:58:45 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-197060128  

> You are right, but people want to know how to define flat adaptors as the ones you have defined.  
  
But there is a lot detail things that goes on now. There is the `detail::callable_base` to handle callable, there is `FIT_INHERIT_CONSTRUCTOR` to handle the constructor, there is the `get_base_function(xs...)` with unused parameters  to handle the base conversion, and there is other things to handle constexpr initialization with MSVC. Having a framework can simplify how the user would define an adaptor, and it can simplify the implementation of adaptors currently in Fit.

---

## Comment 5

> Username: viboes  
> Created at: 2016-03-15 23:49:54 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-197071390  

I'm not against a framework. The framework would need a motivation that should be described by showing how do you define an adaptor without :)

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-03-26 02:06:10 UTC  
> Url: https://github.com/boostorg/hof/issues/150#issuecomment-201667820  

Builders could be provided to build adaptors. Here are the different builders I am thinking about:  
- `unary_adaptor_builder` - This builds an adaptor taking one function  
- `binary_adaptor_builder` - This builds an adaptor taking two functions with an optional unary parameter.  
- `fold_adaptor_builder` - This builds an adaptor taking one or more functions but does a fold over the adaptors(like what is done currently for `conditional`, `compose`, `flow`, etc).  
- `pack_adaptor_builder` - This builds an adaptor taking one or more functions, but `fit::pack` is given for all the functions.  
- `partial_adaptor_builder` - This builds an adaptor taking one function. It works like like `unary_adaptor_builder` except it doesn't invoke the user defined adaptation if the original function is callable.
