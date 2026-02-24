# #764 - Update Travis CI script [Closed]

> Username: jszuppe  
> Created at: 2018-02-19 20:28:54 UTC  
> Updated at: 2018-03-01 15:58:08 UTC  
> Closed at: 2018-03-01 15:58:08 UTC  
> Url: https://github.com/boostorg/compute/issues/764  

Right now CI fails, because Travis CI images were changed/updated and our script is not adjusted to them.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-02-24 20:34:02 UTC  
> Url: https://github.com/boostorg/compute/issues/764#issuecomment-368258045  

I'm still trying to fix Travis CI script. It's always a slow process since you have to wait for Travis CI to do the jobs. I understand that pull requests are on hold because of that issue, and I'll try to at least introduce small working script on Mon.

---

## Comment 2

> Username: ddemidov  
> Created at: 2018-02-24 21:07:58 UTC  
> Url: https://github.com/boostorg/compute/issues/764#issuecomment-368260383  

May be this will help: https://github.com/ddemidov/vexcl/commit/5814e55a84a7f811127b605f14b18598318ce347

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-02-24 21:33:55 UTC  
> Url: https://github.com/boostorg/compute/issues/764#issuecomment-368262007  

I've been focusing more on POCL, but, indeed, that will help.

---

## Comment 4

> Username: jszuppe  
> Created at: 2018-03-01 15:58:07 UTC  
> Url: https://github.com/boostorg/compute/issues/764#issuecomment-369637860  

Fixed in #769 and #770
