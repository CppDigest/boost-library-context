# #64 - differences between partial and decorate [Closed]

> Username: viboes  
> Created at: 2015-10-04 12:29:09 UTC  
> Updated at: 2016-05-19 01:12:49 UTC  
> Closed at: 2016-04-28 03:51:29 UTC  
> Url: https://github.com/boostorg/hof/issues/64  

I don't see the differences between the two function  
  
```  
assert(decorate(f)(x)(g)(xs...) == f(x, g, xs...));  
  
assert(partial(f)(xs...)(ys...) == f(xs..., ys...));  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:59:27 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-145445557  

Since `partial` allows for continual partial application, it can mask errors. So `decorate` does partial application internally, but will produce proper error messages.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:09:45 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-145684170  

I don't understand yet what errors can be masked and how decorate solves the issue.  
This merits some explanations in the documentation

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-09 19:14:46 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-146964643  

Say, for instance that `f(x, g, xs...)` would cause a compile error due to substitution failure. Then this will compile:  
  
``` cpp  
partial(f)(x)(g)(xs...);  
```  
  
This is because `f(x, g, xs...)` is not callable. So partial will return another function because perhaps if more parameters are applied the function will be callable. There is no way in C++ to know that more parameters can't be applied. Although I am thinking of adding a `limit` decorator so it can annotate the max limit of parameters, which `partial` and `pipable` can use to avoid this scenario. However, this doesn't help with decorators which has no max limit.  
  
In contrast with decorators this will cause a compile error if `f(x, g, xs...)` is not callable:  
  
``` cpp  
decorate(f)(x)(g)(xs...);  
```  
  
That is because the decorator know that there is no more partial applications, so if `f(x, g, xs...)` is not callable the above simply fails as well.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-09 19:21:22 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-146965787  

What are `f`, `x`and `xs` in  
  
```  
assert(decorate(f)(x)(g)(xs...) == f(x, g, xs...));  
```  
  
What is the result of `decorate(f)`? What is his signature?

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-09 20:42:45 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-146981538  

> What is the result of decorate(f)?  
  
The decorator.  
  
> What is his signature?  
  
Something that takes a value `x`.

---

## Comment 6

> Username: tzlaine  
> Created at: 2016-02-09 15:11:51 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-181909066  

+1000.  The above discussion just proves that this stuff needs to be documented.

---

## Comment 7

> Username: pfultz2  
> Created at: 2016-02-20 01:28:05 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-186477061  

Ok I added some more documentation to the decorate adaptor, plus an example. Hopefully, it makes sense. There's just a long chain of things that happen(ie the decorate adaptor returns a decorator that returns an adaptor that returns a function). I don't know of a better way to explain it. I hope the example makes it clearer.

---

## Comment 8

> Username: viboes  
> Created at: 2016-02-29 23:59:35 UTC  
> Updated at: 2016-03-06 23:47:50 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-190455704  

Why the decorator `log_f` operator() must have an additional parameter? Why not let those parameter in the creation of the decorator? Or in other words if you accept one why not a variadic number as in ?  
  
```  
assert(3 == log(a, b, c)(sum)(1, 2));  
```

---

## Comment 9

> Username: pfultz2  
> Created at: 2016-03-01 00:11:25 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-190459914  

> Why the decorator log_f operator() must has an additional parameter?  
  
I would like to support this in the future. However, the user will need to describe the parameters in some way.  
  
> Why not let those parameter in the creation of the decorator?  
  
Because the way the decorator is invoked, its not possible to distinguish between `log(a, b)(sum)(1, 2)` and `log(a)(sum)(a, 1, 2)`.  
  
> Or in other words if you accept one why not a variadic number as in ?  
  
Depending on how multiple parameters are supported in the future, it may be possible to support variadic arguments by "packing" them.

---

## Comment 10

> Username: pfultz2  
> Created at: 2016-04-28 03:50:05 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-215302824  

> Why the decorator log_f operator() must have an additional parameter? Why not let those parameter in the creation of the decorator? Or in other words if you accept one why not a variadic number as in ?  
  
This is related to issue #151, which would allow decorators to support variadic parameters.

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-04-28 03:51:29 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-215302970  

I am going to close since there is no other pending issue here.

---

## Comment 12

> Username: viboes  
> Created at: 2016-04-28 16:15:19 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-215481399  

Could you point to the changes you did to fix it?

---

## Comment 13

> Username: pfultz2  
> Created at: 2016-05-19 01:12:49 UTC  
> Url: https://github.com/boostorg/hof/issues/64#issuecomment-220202535  

I have made no changes yet. The changes that are left to be made are in issue #151. Outside of this issue, I see no more pending changing left for this issue, so I have closed it.
