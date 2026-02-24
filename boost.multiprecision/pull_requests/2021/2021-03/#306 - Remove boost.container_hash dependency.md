# #306 Remove boost.container_hash dependency [Merged]

> Username: mborland  
> Created at: 2021-03-14 12:04:52 UTC  
> Updated at: 2021-03-20 18:54:12 UTC  
> Merged at: 2021-03-20 16:49:14 UTC  
> Closed at: 2021-03-20 16:49:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-03-14 12:12:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#issuecomment-798896631  

Remaining dependencies can be [found here](https://github.com/mborland/multiprecision/issues/1). With math tracking towards being consumed in isolation it seems like it would be useful if multiprecision only depended on math.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 14:05:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/306#pullrequestreview-611699934  

📁 include/boost/multiprecision/detail/hash.hpp

```diff
  15 |+ inline void hash_combine(std::size_t& seed) 
  16 |+ { 
  17 |+     (void)seed; // suppress warning: unused parameter 'seed'
```

> Username: jzmaddock  
> Created_at: 2021-03-14 14:05:58 UTC  
> Updated_at: 2021-03-20 08:26:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#discussion_r593906111  

Point of pedantry: couldn't we just not give the parameter a name?


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 14:06:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/306#pullrequestreview-611700001  

📁 include/boost/multiprecision/detail/hash.hpp

```diff
  28 |+ inline std::size_t hash_value(std::error_condition const& v) 
  29 |+ {
  30 |+     std::size_t seed = 0;
```

> Username: jzmaddock  
> Created_at: 2021-03-14 14:06:41 UTC  
> Updated_at: 2021-03-20 08:26:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#discussion_r593906221  

Point of pedantry #2: do we need this specialization?  It would remove one #include.


---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-20 16:35:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#issuecomment-803400814  

This should be good to go. The only build failure is `Received request to deprovision: The request was cancelled by the remote provider.`

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-03-20 16:49:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#issuecomment-803405999  

Thanks Matt!

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-03-20 18:19:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#issuecomment-803438604  

This is weird, no idea why it didn't show up in the PR CI tests: https://app.circleci.com/pipelines/github/boostorg/multiprecision/609/workflows/bda88c58-8a63-4ce5-9f70-aa88102fd551/jobs/1834

---

## Comment 7

> Username: mborland  
> Created_at: 2021-03-20 18:54:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/306#issuecomment-803445219  

Should be resolved by https://github.com/boostorg/multiprecision/pull/309

---
