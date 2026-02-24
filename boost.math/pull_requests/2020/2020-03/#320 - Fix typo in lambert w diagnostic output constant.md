# #320 Fix typo in lambert w diagnostic output constant [Merged]

> Username: Gattag  
> Created at: 2020-03-02 18:43:04 UTC  
> Updated at: 2020-03-04 09:38:52 UTC  
> Merged at: 2020-03-03 12:20:34 UTC  
> Closed at: 2020-03-03 12:20:34 UTC  
> Url: https://github.com/boostorg/math/pull/320  

Fixes #319

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-03-02 18:49:45 UTC  
> Url: https://github.com/boostorg/math/pull/320#issuecomment-593557637  

Thanks! I'll let @pabristow weigh in before merging, but this looks correct to me.

---

## Review 2 [Approved]

> Username: pabristow  
> Created_at: 2020-03-03 12:13:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/320#pullrequestreview-367927660  

Correct - but how on earth did you spot this?  
  
Were you using BOOST_MATH_INSTRUMENT_LAMBERT_W_TERMS to check performance?

---

## Comment 3

> Username: Gattag  
> Created_at: 2020-03-04 06:50:11 UTC  
> Url: https://github.com/boostorg/math/pull/320#issuecomment-594356190  

@pabristow Yeah I really shouldn't have found this, but I wanted to see an implementation of Fukushima's algorithm of approximation and found this code base with it and just luckily noticed that the constants were not the same when reading through it

---

## Comment 4

> Username: pabristow  
> Created_at: 2020-03-04 09:38:52 UTC  
> Url: https://github.com/boostorg/math/pull/320#issuecomment-594418685  

You need to get out more? ;-)

---
