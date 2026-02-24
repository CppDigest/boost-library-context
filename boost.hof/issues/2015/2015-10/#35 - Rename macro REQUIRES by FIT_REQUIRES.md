# #35 - Rename macro REQUIRES by FIT_REQUIRES [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:27:42 UTC  
> Updated at: 2015-10-30 22:58:46 UTC  
> Closed at: 2015-10-30 22:58:45 UTC  
> Url: https://github.com/boostorg/hof/issues/35  



---

## Comment 1

> Username: ldionne  
> Created at: 2015-10-04 01:34:56 UTC  
> Url: https://github.com/boostorg/hof/issues/35#issuecomment-145305407  

Actually, shouldn't it be `BOOST_FIT_REQUIRES` if the library is to be Boostified?

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-04 15:13:36 UTC  
> Url: https://github.com/boostorg/hof/issues/35#issuecomment-145357181  

Of course, but I don't know if we want to require the change of the namespace and the prefixes  before the review. What do you think? Paul? Do you plan to do this before the review?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 04:34:49 UTC  
> Url: https://github.com/boostorg/hof/issues/35#issuecomment-145428094  

`REQUIRES` is not defined by the library, and is only defined in the example. Although, it might be better to change the example so it doesn't require using `enable_if`.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 22:43:44 UTC  
> Url: https://github.com/boostorg/hof/issues/35#issuecomment-145690064  

Agreed.

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-30 22:58:45 UTC  
> Url: https://github.com/boostorg/hof/issues/35#issuecomment-152669797  

This is not applicable anymore with the latest docs.
