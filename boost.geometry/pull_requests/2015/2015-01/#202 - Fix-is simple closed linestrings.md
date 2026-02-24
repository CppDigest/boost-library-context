# #202 Fix/is simple closed linestrings [Merged]

> Username: mkaravel  
> Created at: 2015-01-28 10:58:56 UTC  
> Updated at: 2015-02-03 19:46:00 UTC  
> Merged at: 2015-01-28 22:21:59 UTC  
> Closed at: 2015-01-28 22:21:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/202  

`bg::is_simple` was handling incorrectly simple closed linestrings within multilinestrings. For example the geometry with WKT `MULTILINESTRING((0 0,10 0,10 10,0 10,0 0))` was failing.  
  
The reason is that the linestring was accepted as a valid linestring, and then for verifying the validity of the multi-linestring, the self-turns of the multi-linestring were computed; the closing point of the linestring was reported as a turn which was wrongly considered as an unacceptable turn.  
  
In this PR such turns are checked explicitly and reported as acceptable.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-28 11:18:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/202#issuecomment-71817996  

I'm OK with merging this

---
