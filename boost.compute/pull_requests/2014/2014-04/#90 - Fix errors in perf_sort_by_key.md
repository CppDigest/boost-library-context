# #90 Fix errors in perf_sort_by_key [Merged]

> Username: roshanrags  
> Created at: 2014-04-18 05:23:28 UTC  
> Updated at: 2014-04-18 16:28:29 UTC  
> Merged at: 2014-04-18 15:15:08 UTC  
> Closed at: 2014-04-18 15:15:08 UTC  
> Url: https://github.com/boostorg/compute/pull/90  

Fixes #84.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-04-18 06:13:53 UTC  
> Url: https://github.com/boostorg/compute/pull/90#issuecomment-40789424  

Hmm, I had trouble building this.  
  
In `perf_sort_by_key.cpp` you should include `<boost/compute/types/builtin.hpp>` and then at the top of the `main()` function add the following:  
  
```  
using boost::compute::int_;  
using boost::compute::long_;  
```

---

## Comment 2

> Username: roshanrags  
> Created_at: 2014-04-18 06:48:35 UTC  
> Url: https://github.com/boostorg/compute/pull/90#issuecomment-40790617  

Fixed.

---

## Comment 3

> Username: coveralls  
> Created_at: 2014-04-18 07:02:17 UTC  
> Url: https://github.com/boostorg/compute/pull/90#issuecomment-40791198  

[![Coverage Status](https://coveralls.io/builds/692359/badge)](https://coveralls.io/builds/692359)  
  
Coverage remained the same when pulling **f491c55dbd6420a88f53cca2d6c7b40998388db2 on roshanr95:perf_sort_by_key** into **2fb2c08806a0ca7839b15d04e7650bcfffacf820 on kylelutz:develop**.

---

## Comment 4

> Username: roshanrags  
> Created_at: 2014-04-18 07:14:30 UTC  
> Url: https://github.com/boostorg/compute/pull/90#issuecomment-40791647  

On a side note, why don't we add the building of benchmarks and examples to Travis though they are not useful for testing as such. This would ensure that the benchmarks and examples atleast compile properly.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-04-18 15:34:13 UTC  
> Url: https://github.com/boostorg/compute/pull/90#issuecomment-40817656  

That's a good idea. Done in 5fb9130146d373b694bc379ef201714d0f831bec

---
