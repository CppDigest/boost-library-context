# #125 Partition point [Merged]

> Username: roshanrags  
> Created at: 2014-05-28 22:05:30 UTC  
> Updated at: 2014-06-04 23:10:42 UTC  
> Merged at: 2014-05-30 06:16:03 UTC  
> Closed at: 2014-05-30 06:16:03 UTC  
> Url: https://github.com/boostorg/compute/pull/125  

Added partition_point algorithm and test

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-28 22:39:25 UTC  
> Url: https://github.com/boostorg/compute/pull/125#issuecomment-44473932  

[![Coverage Status](https://coveralls.io/builds/813227/badge)](https://coveralls.io/builds/813227)  
  
Coverage increased (+0.01%) when pulling **c8d4836a6f7c15e1d489b6366a5979117f917f31 on roshanr95:partition_point** into **8cfaf793f6e9eda2eb1c78ebf8f6dc98ccc33f52 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-05-29 04:57:37 UTC  
> Updated_at: 2014-05-29 17:18:07 UTC  
> Url: https://github.com/boostorg/compute/pull/125#discussion_r13169077  

so here calling `*position` will create a separate command queue just to copy the single value. it would be better to use the `read_single_value()` function and pass it the same queue object this function was passed.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-05-29 04:58:36 UTC  
> Url: https://github.com/boostorg/compute/pull/125#issuecomment-44494558  

Looks good! One small comment and then it should be ready to merge.

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-05-29 17:59:23 UTC  
> Url: https://github.com/boostorg/compute/pull/125#issuecomment-44563119  

[![Coverage Status](https://coveralls.io/builds/815683/badge)](https://coveralls.io/builds/815683)  
  
Coverage increased (+0.01%) when pulling **79d374f646f19fc28a410c50c2081bf869c421df on roshanr95:partition_point** into **8cfaf793f6e9eda2eb1c78ebf8f6dc98ccc33f52 on kylelutz:develop**.

---

## Comment 5

> Username: roshanrags  
> Created_at: 2014-05-29 21:09:27 UTC  
> Url: https://github.com/boostorg/compute/pull/125#issuecomment-44585447  

Done!

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-05-30 06:16:08 UTC  
> Url: https://github.com/boostorg/compute/pull/125#issuecomment-44618463  

Thanks. Merged!

---
