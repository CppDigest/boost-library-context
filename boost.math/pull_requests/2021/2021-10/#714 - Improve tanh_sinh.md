# #714 Improve tanh_sinh. [Merged]

> Username: jzmaddock  
> Created at: 2021-10-25 17:30:12 UTC  
> Updated at: 2021-11-03 20:12:48 UTC  
> Merged at: 2021-11-03 19:54:45 UTC  
> Closed at: 2021-11-03 19:54:45 UTC  
> Url: https://github.com/boostorg/math/pull/714  

tanh_sinh: improve thrash detection.  
  
Significantly reduces the number of iterations in many cases.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-10-25 17:30:34 UTC  
> Url: https://github.com/boostorg/math/pull/714#issuecomment-951146610  

Work in progress for https://github.com/boostorg/math/issues/706

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-11-03 17:51:29 UTC  
> Url: https://github.com/boostorg/math/pull/714#issuecomment-959778546  

This is a major improvement to tanh_sinh. Is it realistic to get it into 1.78?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-11-03 19:52:10 UTC  
> Url: https://github.com/boostorg/math/pull/714#issuecomment-959902209  

> This is a major improvement to tanh_sinh. Is it realistic to get it into 1.78?  
  
That's a good question.  I still have further improvements lurking on my HD (pruning the tails when they are trivially small), but I need to test them at various different precisions before signing off on those.  
  
I'm also not completely convinced we don't have a bug somewhere, something doesn't seem quite right, but I don't see what.  
  
That said, maybe we should merge what we have and continue this at a later date.

---
