# #60 Added CI files [Merged]

> Username: Flast  
> Created at: 2018-01-01 04:53:19 UTC  
> Updated at: 2018-01-19 21:34:48 UTC  
> Merged at: 2018-01-19 19:02:57 UTC  
> Closed at: 2018-01-19 19:02:57 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60  



---

## Comment 1

> Username: djowel  
> Created_at: 2018-01-01 04:59:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-354636747  

wonderful!

---

## Comment 2

> Username: Flast  
> Created_at: 2018-01-02 08:07:24 UTC  
> Updated_at: 2018-01-02 08:14:18 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-354717544  

Note that failure of bug4853 is caused by boostorg/range#54

---

## Comment 3

> Username: Flast  
> Created_at: 2018-01-05 02:46:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-355459130  

Hmm... Travis exceeds its running time limit, so we need breaking a job...

---

## Comment 4

> Username: Flast  
> Created_at: 2018-01-15 14:51:08 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-357703871  

I reorganized (and temporary disabled some) lambda_tests, so I hope that CIs are passed.

---

## Review 5 [Commented]

> Username: Flast  
> Created_at: 2018-01-16 00:38:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/60#pullrequestreview-88961011  

📁 test/scope/lambda_tests10.cpp

```diff
  19 | int main()
  20 | {
  21 |+ #if defined(RUNNING_ON_APPVEYOR) && BOOST_WORKAROUND(BOOST_MSVC, == 1700)
```

> Username: Flast  
> Created_at: 2018-01-16 00:38:48 UTC  
> Updated_at: 2018-01-16 00:51:24 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#discussion_r161639407  

Oops, it should be 1800 (1700 is msvc-11).


---

## Comment 6

> Username: Flast  
> Created_at: 2018-01-19 19:02:35 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-359058926  

Yeah! Now all CIs are passed.

---

## Comment 7

> Username: djowel  
> Created_at: 2018-01-19 21:34:48 UTC  
> Url: https://github.com/boostorg/phoenix/pull/60#issuecomment-359095455  

Wonderful!

---
