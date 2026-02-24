# #226 [map] Use an eager algorithm for map lookup [Closed]

> Username: ricejasonf  
> Created at: 2015-12-20 18:49:52 UTC  
> Updated at: 2016-02-16 20:27:09 UTC  
> Closed at: 2016-02-16 20:27:09 UTC  
> Url: https://github.com/boostorg/hana/pull/226  

Related #223

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2015-12-24 23:56:09 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-167168979  

There may be more room for improvement. I'm not sure. After rebasing the benchmarks seemed to run .01-.02 seconds slower at n=400.   
  
Using the specialization for type_tag in `at_key` doesn't seem to make a difference so maybe I am doing something wrong there.

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2015-12-26 00:18:27 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-167268219  

On my box, `benchmark.at_key.lookup.compile` shows map compiling in 1.38s for every test. It also reveals that `hana::set` scales linearly.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2015-12-26 03:16:32 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-167274277  

2911e959ffca7cda0dcac97e3b0f4823ce8308e2 removes some vestigal helpers I accidentally left in.  
  
I tried having `make` create `map_impl` directly, but there was no noticeable performance difference.

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2016-01-03 01:27:26 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-168448850  

For `benchmark.at_key.lookup.compile`, I substituted a lightweight Product for the map which puts it on par with tuple and set. (0.5s on my box)

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-01-21 00:02:34 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-173407532  

OK, so I updated your branch with my modifications.

---

## Comment 6

> Username: ldionne  
> Created_at: 2016-02-16 20:27:09 UTC  
> Url: https://github.com/boostorg/hana/pull/226#issuecomment-184863114  

This is superseded by #247, which shows better compile-time performance.

---
