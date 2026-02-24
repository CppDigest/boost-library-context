# #63 - capture versus partial [Closed]

> Username: viboes  
> Created at: 2015-10-04 10:18:28 UTC  
> Updated at: 2015-10-30 23:10:16 UTC  
> Closed at: 2015-10-30 23:10:16 UTC  
> Url: https://github.com/boostorg/hof/issues/63  

```  
capture(xs...)(f)(ys...) <==> f(xs..., ys...)  
```  
  
Another way is to define it as  
  
```  
capture(xs...)(f) <==> partial(f, xs...)  
```  
  
Is there a difference in performances, between the 3 expressions?  
  
```  
capture(xs...)(f)(ys...) <==> f(xs..., ys...) <==> partial(f, xs...)(ys...)  
```  
  
BTW, the example would clearer if you use different number   
  
```  
assert(f(5, 8) == std::make_tuple(std::make_pair(1, 5), std::make_pair(2, 8)));  
```  
  
or even different types.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:56:10 UTC  
> Url: https://github.com/boostorg/hof/issues/63#issuecomment-145445265  

There not the same. It is **almost** like this:  
  
``` cpp  
capture_decay(xs...)(f)(ys...) <==> partial(f)(xs...)(ys...)  
```  
  
However, `partial` allows for continual partial application. So if `partial(f)(xs...)(ys...)` is not callable, you will always get another function back, whereas if `capture_decay(xs...)(f)(ys...)` is not callable, you will get a compile error. Plus, with `capture` you have three different options of how variables are captures. With `partial` it is always uses `fit::decay` which is the safest option.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:13:33 UTC  
> Url: https://github.com/boostorg/hof/issues/63#issuecomment-145684871  

Any comment about the performances?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-09 20:45:53 UTC  
> Url: https://github.com/boostorg/hof/issues/63#issuecomment-146982115  

Well `capture_decay` and `partial` there should be no difference in runtime performance since they both copy the parameters, however, `capture` and `capture_forward` can be used to avoid extraneous copies.
