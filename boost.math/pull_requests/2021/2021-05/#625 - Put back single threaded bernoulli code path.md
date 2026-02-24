# #625 Put back single threaded bernoulli code path [Merged]

> Username: mborland  
> Created at: 2021-05-08 18:39:00 UTC  
> Updated at: 2021-05-09 13:00:47 UTC  
> Merged at: 2021-05-09 12:23:25 UTC  
> Closed at: 2021-05-09 12:23:25 UTC  
> Url: https://github.com/boostorg/math/pull/625  

There is a `#undef BOOST_HAS_THREADS` to verify the single thread path with CI. Will remove before requesting merge.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-05-09 11:11:52 UTC  
> Url: https://github.com/boostorg/math/pull/625#issuecomment-835783871  

@jzmaddock Pending review this should be good to go. It is not much more than copying and pasting the code that used to be in here.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-09 12:23:20 UTC  
> Url: https://github.com/boostorg/math/pull/625#issuecomment-835796462  

Thanks again Matt!

---
