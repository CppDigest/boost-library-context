# #22 Feature/setops [Merged]

> Username: mkaravel  
> Created at: 2014-05-05 09:14:43 UTC  
> Updated at: 2014-05-07 09:58:11 UTC  
> Merged at: 2014-05-07 09:58:11 UTC  
> Closed at: 2014-05-07 09:58:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/22  

(1) stylistic change: rename a functor  
(2) re-write existing test cases with degenerate geometries as separate test cases  
(3) modify copy segments and follow code to allow the output of spikes  
(4) add more tests with degenerate geometries and geometries with spikes

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-06 14:49:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/22#issuecomment-42312085  

Would it be possible to include only those parts of TypeTraits that are actually used? E.g. in copy_segments.hpp  
  
Btw which type trait is used there? I couldn't find any  
  
A general comment not related to this pull request.  
Some time ago I added detail::normalized_view<> which connects closeable_view<> and reversible_view<> (https://github.com/boostorg/geometry/commit/6bbabf986708f03c33972eb0a65c9963cf29f6a0).  
  
E.g. 20-line code defining views (in copy_segments.hpp, copy_segments_ring<>, line 64) could be replaced by:  
  
```  
detail::normalized_view<Geometry const> const range(rng);  
```

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-06 18:55:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/22#issuecomment-42343767  

Just modified (and pushed) the change. The type trait used is integral constant (see line 167).  
  
Regarding the other issue (normalized_view), I totally agree. I think it would be best though to do after we are done with pending issues. Good to remind about it though. It can be used for new code.

---
