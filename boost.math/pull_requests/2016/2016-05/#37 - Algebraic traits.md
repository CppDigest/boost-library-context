# #37 Algebraic traits [Closed]

> Username: jeremy-murphy  
> Created at: 2016-05-03 13:04:58 UTC  
> Updated at: 2017-11-06 14:42:35 UTC  
> Closed at: 2017-11-06 14:42:35 UTC  
> Url: https://github.com/boostorg/math/pull/37  

Just a rough cut of an idea for algebraic traits. Not intended for merging (yet), just for discussion, so don't mind the file locations or names of things.  
  
The traits classes have a couple of different ideas running through them so there is inconsistency around how to provide default values. I was also vexed by wanting an interface like `algebraic_traits<op<T>>` but settled for `algebraic_traits<op, T>`.  
  
But the end result is `gcd_range` that was simple to implement in terms of `accumulate_if` using a specialization of `algebraic_traits` for `gcd_evaluator`.  
  
Thoughts?

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-05-04 00:39:24 UTC  
> Url: https://github.com/boostorg/math/pull/37#issuecomment-216710449  

Maybe another way this could be done is to have one class per abstraction -- magma, group, ring, etc -- and use `numeric_limits` to pick the appropriate one.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2016-05-06 03:20:37 UTC  
> Updated_at: 2016-05-06 03:20:56 UTC  
> Url: https://github.com/boostorg/math/pull/37#issuecomment-217339216  

I should mention the excellent technical report, [TR638](http://www.cs.indiana.edu/pub/techreports/TR638.pdf), that Peter Gottschling wrote back in 2006. It's a detailed look at implementing algebraic concepts in C++. I don't plan to be quite that ambitious, as I assume we can have and find use for traits without implementing concepts.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-05-06 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/37#issuecomment-217502097  

My gut feeling here is that this is a bit of a "sledgehammer to crack a nut"?  I'd much rather keep this simple if possible.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-05-06 23:23:21 UTC  
> Url: https://github.com/boostorg/math/pull/37#issuecomment-217586051  

Right. My point here is not that we can go to great lengths just to implement `gcd_range`, but that inside it is a generic algorithm and traits facility waiting to get out that could be the used in the implementation of many other algorithms. So I don't mean this PR to have any bearing on the work we're doing on gcd at the moment, it was just the example that inspired it.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2017-11-06 14:42:35 UTC  
> Url: https://github.com/boostorg/math/pull/37#issuecomment-342168841  

I still really like this idea, but I'll bring it back when it has more substance.

---
