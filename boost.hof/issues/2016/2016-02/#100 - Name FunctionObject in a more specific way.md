# #100 - Name FunctionObject in a more specific way [Closed]

> Username: viboes  
> Created at: 2016-02-29 23:28:00 UTC  
> Updated at: 2016-03-06 23:59:39 UTC  
> Closed at: 2016-03-01 17:05:04 UTC  
> Url: https://github.com/boostorg/hof/issues/100  

Matt Calabrese wrote:  
  
> I suggest not using the name FunctionObject for something that is only  
> callable with a const object. This is a little more specific that what most  
> people refer to as a function object in C++ and may cause confusion.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-01 00:02:47 UTC  
> Url: https://github.com/boostorg/hof/issues/100#issuecomment-190456834  

I am thinking of describing it as `ConstFunctionObject`, this also affects the `Callable` concept as well.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-01 18:04:50 UTC  
> Url: https://github.com/boostorg/hof/issues/100#issuecomment-190835682  

I'm for ConstFunctionObject as you have also MutableFunctionObject.
