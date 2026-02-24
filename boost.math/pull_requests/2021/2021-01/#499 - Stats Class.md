# #499 Stats Class [Closed]

> Username: mborland  
> Created at: 2021-01-23 18:56:58 UTC  
> Updated at: 2024-02-22 10:25:53 UTC  
> Closed at: 2024-02-22 10:25:53 UTC  
> Url: https://github.com/boostorg/math/pull/499  

My cut at implementing the statistics class outlined in [P1708](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1708r2.pdf). Until meta-classes are added there is not an obvious (to me) way to offer more interfaces under `stats` instead of resorting to using `integer_stats`

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-01-23 19:00:05 UTC  
> Url: https://github.com/boostorg/math/pull/499#issuecomment-766161540  

@mborland : Is the goal to just implement the spec listed above? I'm just not sure what the benefit of this is over the stats in boost/accumulators and therefore I feel like this PR should maybe go directly into libstdc++.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-01-23 19:11:18 UTC  
> Url: https://github.com/boostorg/math/pull/499#issuecomment-766163683  

It is derived from the spec above since `univariate_statistics` has more functionality than the proposal. The earliest something similar would go into the language standard is 2023, so in the meantime it seems like a pretty ergonomic way to using the math stats library.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-01-23 20:11:19 UTC  
> Url: https://github.com/boostorg/math/pull/499#issuecomment-766172313  

> The earliest something similar would go into the language standard is 2023  
  
It could go in an `#include <experimental/stats.hpp>` no?

---

## Comment 4

> Username: mborland  
> Created_at: 2021-01-24 07:20:01 UTC  
> Url: https://github.com/boostorg/math/pull/499#issuecomment-766304413  

> > The earliest something similar would go into the language standard is 2023  
>   
> It could go in an `#include <experimental/stats.hpp>` no?  
  
It could, but aren't the authors still at the mercy of the LEWG/WG21 to push the proposal far enough forward to get to that point? I would say this is more akin to filesystem, type_traits, etc. where a similar or expanded version exists in boost first that can be transplanted into the STL when the time comes.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2021-01-24 15:19:35 UTC  
> Url: https://github.com/boostorg/math/pull/499#issuecomment-766366596  

I do worry that this will bloat our own library. The single-pass, parallel, numerically stable stats routines are a compelling feature, and I don't see how this really improves on that. (The reason for the bitset to specify which stats are wanted was to allow for computation of all desired stats in one pass, but this doesn't exploit that.)  
  
I'll let @jzmaddock make a decision on this one, there's a case for it I admit, but it's really not something I'm interested in since it appears to be syntactic sugar on top of what we already have.

---
