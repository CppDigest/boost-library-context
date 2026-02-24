# #78 - repeat - what is the rationale of using integral_contant as parameter? [Closed]

> Username: viboes  
> Created at: 2015-10-04 17:38:28 UTC  
> Updated at: 2016-03-07 00:03:25 UTC  
> Closed at: 2016-02-21 18:02:37 UTC  
> Url: https://github.com/boostorg/hof/issues/78  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-09 16:28:50 UTC  
> Url: https://github.com/boostorg/hof/issues/78#issuecomment-146921282  

Actually, I would like to support runtime values in the future. I used an `integral_constant` to make it consistent with `repeat_while`. Using `integral_constant` allows stopping compilation of the function when repeat stops, which can avoid compilation errors or overflows. I am going to keep this issue open as reminder to implement this for runtime integrals.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-09 17:24:07 UTC  
> Url: https://github.com/boostorg/hof/issues/78#issuecomment-146938532  

I understand the compile-time advantage, but why `ìntegral_constant` and not just an `int`?

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-02-21 18:02:37 UTC  
> Url: https://github.com/boostorg/hof/issues/78#issuecomment-186874955  

`repeat` now supports integrals.
