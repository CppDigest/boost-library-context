# #69 Improve performance of inner_product [Closed]

> Username: roshanrags  
> Created at: 2014-03-19 00:10:48 UTC  
> Updated at: 2014-04-12 11:05:40 UTC  
> Closed at: 2014-03-24 01:23:43 UTC  
> Url: https://github.com/boostorg/compute/pull/69  

Implemented simple kernel to perform inner_product, 10x performance gain

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-03-19 00:49:38 UTC  
> Url: https://github.com/boostorg/compute/pull/69#issuecomment-38006851  

[![Coverage Status](https://coveralls.io/builds/604367/badge)](https://coveralls.io/builds/604367)  
  
Coverage remained the same when pulling **ab03b7891bf9dcb399b5b39232607301730f5021 on roshanr95:inner_product** into **fdadf85334d5e6d2ac4643b2e44031271cc51186 on kylelutz:develop**.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-03-19 05:57:24 UTC  
> Updated_at: 2014-03-19 05:58:16 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10737976  

Should not this change **decrease** the performance? It looks like the original code accumulated the transformed sequence on the fly, and the new version stores the intermediate result to the global memory.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-03-19 07:15:15 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10738963  

There is an optimized accumulate/reduce() implementation which is used when the input iterators are buffer_iterator's and the function is commutative. This is most likely the cause for the performance increase.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2014-03-19 07:21:44 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10739085  

I see. So the best way to increase performance of `inner_product` (and probably of other operations and also user code) would be to optimize the implementation of `accumulate` for the transform iterators. `accumulate` should only read its input once, so I don't see why input being a buffer is important here?

---

## Comment 5

> Username: roshanrags  
> Created_at: 2014-03-19 09:40:47 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10742082  

Accumulating the transformed sequence on the fly leads to a serial accumulation. As @kylelutz said, using a buffer eventually leads to reduce() or reduce_on_gpu() which is much faster.

---

## Comment 6

> Username: ddemidov  
> Created_at: 2014-03-19 10:00:28 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10742756  

Yes, but the proposed variant uses four unnecessary global memory IO operations (write `a`, `b`, `c`, read `c`). I am sure this is the reason why it is still slower than the STL version. It should be easy to extend the parallel version of `accumulate` to work with generic random access iterators.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-03-19 15:08:36 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10753109  

Yeah, the proper long-term fix is to optimize accumulate/reduce for all input iterators, not just buffer_iterator.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-03-19 15:14:08 UTC  
> Url: https://github.com/boostorg/compute/pull/69#discussion_r10753383  

I've added an issue (#73) to fix the performance of `accumulate()` with `InputIterator`s. I'll look into making the fix which should greatly improve the performance of current `inner_product()` implementation.

---

## Comment 9

> Username: roshanrags  
> Created_at: 2014-03-24 01:23:43 UTC  
> Url: https://github.com/boostorg/compute/pull/69#issuecomment-38405059  

Better fixed by issue #73.

---

## Comment 10

> Username: kylelutz  
> Created_at: 2014-04-11 05:38:04 UTC  
> Url: https://github.com/boostorg/compute/pull/69#issuecomment-40172282  

I've updated `reduce()` to work with generic iterators which drastically speeds up the `perf_inner_product` benchmark.  
  
@roshanr95: Can you try out the code from develop?

---

## Comment 11

> Username: roshanrags  
> Created_at: 2014-04-12 11:05:40 UTC  
> Url: https://github.com/boostorg/compute/pull/69#issuecomment-40277708  

Working great!! ~160x performance increase from original.

---
