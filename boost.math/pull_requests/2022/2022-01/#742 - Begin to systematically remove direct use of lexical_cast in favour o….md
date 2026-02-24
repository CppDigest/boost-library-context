# #742 Begin to systematically remove direct use of lexical_cast in favour o… [Merged]

> Username: jzmaddock  
> Created at: 2022-01-16 17:37:20 UTC  
> Updated at: 2024-01-24 21:34:10 UTC  
> Merged at: 2022-01-19 18:40:11 UTC  
> Closed at: 2022-01-19 18:40:11 UTC  
> Url: https://github.com/boostorg/math/pull/742  

…f existing abstractions.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-01-16 17:39:36 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1013919791  

@mborland : this began as the "simple" task of revising unchecked_factorial.hpp and trying to remove your lexical_cast workaround to see what failed.... it's grown rather larger into replacing lots of lexical_cast usages in favour of existing abstractions.  More soon...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-01-18 17:09:15 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1015628300  

One stalled test, merging.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-01-18 17:12:24 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1015631266  

On second thoughts this is a big diff, I'll wait and see if anyone wants to comment.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-01-18 17:21:01 UTC  
> Updated_at: 2022-01-18 17:21:45 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1015638902  

>> this began as the "simple" task of revising unchecked_factorial.hpp and trying to remove your lexical_cast workaround to see what failed.  
  
> On second thoughts this is a big diff, I'll wait and see if anyone wants to comment.  
  
For a few days, I've been meaning to address a possibly related issue. At the time of writing this post, the rework of `unchecked_factorial.hpp` broke my nightly build in [ckormanyos/wide-decimal](https://github.com/ckormanyos/wide-decimal), a repository which serves, among other purposes, to provide an incubator for `cpp_dec_float`.  
  
Anyway, all my [jobs failed](https://github.com/ckormanyos/wide-decimal/actions/runs/1700320696) because [this line](https://github.com/boostorg/math/blob/4eb1db813374c8365f16fe4537bb692b0eb8ae5d/include/boost/math/special_functions/detail/unchecked_factorial.hpp#L672) prevents my math-bindings from getting `unchecked_factorial()`.  
  
Ordinarily I would just patch my sub-project. And ordinarily-ordinarily I'd write a full Multiprecision backend instead of just bindings.  
  
But the point is --- something changed on develop branch where I run nightlies. So I feel it's worth addressing.  
  
I did not yet see if John's rework here addresses this problem.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-01-18 17:29:28 UTC  
> Updated_at: 2022-01-18 17:29:47 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1015646606  

> did not yet see if John's rework here addresses this problem.  
  
Sorry about the noise, ... the `lexical_cast_fixes` branch does, in fact, seem to fix the phenomenon I had addressed above.  
  
As a final comment... does [this line](https://github.com/boostorg/math/blob/9d8f5b107c747c57a5228adfa2f9e44d4110d1be/include/boost/math/special_functions/detail/unchecked_factorial.hpp#L697) serve any purpose. I am torn and confused by it. On the one hand, i think only user-defined types will end up instantiating those templates, none of which will have any meaning with `<type_traits>`'s battery of `std::is_`-whatever. On the other hand, i can't really _figure out_ what all types will be instantiated there.  
  
Cc: @jzmaddock and @mborland and @NAThompson.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-01-18 19:17:46 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1015743099  

> As a final comment... does this line serve any purpose. I am torn and confused by it. On the one hand, i think only user-defined types will end up instantiating those templates, none of which will have any meaning with <type_traits>'s battery of std::is_-whatever. On the other hand, i can't really figure out what all types will be instantiated there.  
  
Regular floating point types (float, double, long double and __float128) are addressed in other specializations, so yes, this template was intended for multiprecision types only.  However, there is a bug report somewhere that if you're foolish enough to instantiate `unchecked_factorial<unsigned>` then you also end up here.  Since you really shouldn't be using integer types with this template (unless we choose to extend support to unbounded multiprecision integer types), thes static_asserts are here to catch this misuse.  Unfortunately the previous version was incomplete and had a terrible error message :(  
  
So... I'll push some comments to that effect.

---

## Comment 7

> Username: mborland  
> Created_at: 2022-01-19 18:09:30 UTC  
> Url: https://github.com/boostorg/math/pull/742#issuecomment-1016732523  

@jzmaddock This looks good to me. Thanks for removing my workarounds.

---
