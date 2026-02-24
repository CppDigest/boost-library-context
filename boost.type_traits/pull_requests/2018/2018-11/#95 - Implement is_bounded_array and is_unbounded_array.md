# #95 Implement is_bounded_array and is_unbounded_array [Merged]

> Username: glenfe  
> Created at: 2018-11-08 04:51:37 UTC  
> Updated at: 2019-01-07 18:34:41 UTC  
> Merged at: 2019-01-07 18:34:41 UTC  
> Closed at: 2019-01-07 18:34:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/95  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-01-07 05:26:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/95#pullrequestreview-189680302  

📁 doc/is_unbounded_array.qbk

```diff
  25 |+ [:`is_unbounded_array<int[]>` inherits from `__true_type`.]
  26 |+ 
  27 |+ [:`is_unbounded_array<char[][]>::type` is the type `__true_type`.]
```

> Username: pdimov  
> Created_at: 2019-01-07 05:26:55 UTC  
> Updated_at: 2019-01-07 05:30:27 UTC  
> Url: https://github.com/boostorg/type_traits/pull/95#discussion_r245551895  

`char[][]`?


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-01-07 18:34:34 UTC  
> Url: https://github.com/boostorg/type_traits/pull/95#issuecomment-452035115  

Looks good to me now, thanks @pdimov for spotting the typo!

---
