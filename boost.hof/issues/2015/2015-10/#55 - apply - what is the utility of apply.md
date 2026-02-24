# #55 - apply - what is the utility of apply? [Open]

> Username: viboes  
> Created at: 2015-10-04 02:21:21 UTC  
> Updated at: 2016-03-06 23:53:25 UTC  
> Url: https://github.com/boostorg/hof/issues/55  

Oh, I believe I have found why I don't understand. The template parameters TS are not part of the function apply   
This  
  
```  
template<class F, class... Ts>  
constexpr auto apply(F&& f, Ts&&... xs);  
```  
  
should be  
  
```  
template<class F>  
constexpr auto apply(F&& f);  
```  
  
The semantics given us  
  
```  
assert(apply(f)(xs...) == f(xs...));  
```  
  
But the usage is incompatible  
  
```  
assert(fit::apply(sum_f(), 1, 2) == 3);  
```  
  
that I believe should be  
  
``` c££  
assert(fit::apply(sum_f())(1, 2) == 3);  
```  
  
Unfortunately the implementation is not inline at all.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:22:31 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-145441434  

Oops, thats a mistake in the documentation. The semantics should be:  
  
``` cpp  
assert(apply(f, xs...) == f(xs...));  
```

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 06:27:08 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-145442000  

So the question remains, what is the utility of this function?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 07:31:01 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-145449728  

That you want to call the first parameter with the rest of the parameters. One example, is to curry the functions parameters:  
  
``` cpp  
assert(compress(apply, f)(x, y, z) == f(x)(y)(z));  
```

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 22:29:22 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-145687524  

Ok, I see `apply` is the monoid operator on function composition.   
  
Could you add this example or another more user friendly?

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-05 22:54:55 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-145691782  

> Could you add this example or another more user friendly?   
  
Yea I will.

---

## Comment 6

> Username: viboes  
> Created at: 2016-03-01 00:05:49 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-190457957  

The semantics must be fixed yet.

---

## Comment 7

> Username: pfultz2  
> Created at: 2016-03-01 00:13:14 UTC  
> Url: https://github.com/boostorg/hof/issues/55#issuecomment-190461165  

Oh, should I add that to the "semantics" of `apply`? That might be a good idea.
