# #46 - What is the rationale for args been 1 based? [Closed]

> Username: viboes  
> Created at: 2015-10-04 02:00:12 UTC  
> Updated at: 2015-10-05 22:35:25 UTC  
> Closed at: 2015-10-05 22:35:25 UTC  
> Url: https://github.com/boostorg/hof/issues/46  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 05:01:06 UTC  
> Url: https://github.com/boostorg/hof/issues/46#issuecomment-145430615  

It is used by the placeholders. However, it may be better to have `args` be zero based.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:35:25 UTC  
> Url: https://github.com/boostorg/hof/issues/46#issuecomment-145688696  

After some more thought it is a good thing the arguments start at 1, as the 0-th argument has no sense.  
Anyway do as you believe it is better.
