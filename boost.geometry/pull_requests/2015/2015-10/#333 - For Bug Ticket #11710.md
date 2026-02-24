# #333 [geometry][algorithm] For Bug Ticket #11710  [Closed]

> Username: scopeInfinity  
> Created at: 2015-10-21 12:16:14 UTC  
> Updated at: 2015-11-06 11:37:43 UTC  
> Closed at: 2015-11-06 11:37:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/333  

**is_simple** - now return **true** for all empty geometry

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-10-22 13:05:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/333#issuecomment-150213293  

@gagan1kumar  Thank you for the PR. AFAIR, only empty multi-geometries are considered valid, and in that respect it would make sense to me to be consistent with that approach. I would return `true` only for empty multi-geometries.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-11-05 13:17:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/333#issuecomment-154056991  

@gagan1kumar I have addressed the issue in PR #334 (only for multi-geometries as I describe in my previous comment). Empty linestrings, rings and polygons are currently considered as invalid by `bg::is_valid()` which means that the behavior of `bg::is_simple()` for those inputs is undefined. This is a general rule we follow in BG: algorithms (with the obvious exception of `bg::is_valid()`) are assuming valid input; if invalid input is passed through, the behavior of the BG algorithm is defined, and in general we do our best to produce a sensible result.  
  
Could you please close this PR?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-11-05 19:52:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/333#issuecomment-154171759  

OK for me to close.  
Besides this (note to @gagan1kumar ): never create PR from/for master, always for develop branch  
But thanks anyway for this PR (even if it is fixed otherwise)

---
