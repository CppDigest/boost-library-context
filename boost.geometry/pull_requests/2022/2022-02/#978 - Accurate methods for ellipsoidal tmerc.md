# #978 [srs] Accurate methods for ellipsoidal tmerc [Merged]

> Username: vissarion  
> Created at: 2022-02-18 14:23:41 UTC  
> Updated at: 2022-04-08 10:22:37 UTC  
> Merged at: 2022-04-08 10:22:32 UTC  
> Closed at: 2022-04-08 10:22:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/978  

This PR adds exact (or Poder/Engsager) implementations for ellipsoidal `tmerc` projection. It fixes several tests that had wrong/inaccurate results. Currently the old implementation (approximate or Evenden/Snyder) is commented out.   
  
For reference see https://github.com/OSGeo/PROJ/blob/master/src/projections/tmerc.cpp

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-02-18 14:35:32 UTC  
> Updated_at: 2022-02-18 14:37:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/978#issuecomment-1044622542  

LGTM. You should probably update the version of proj which this update is from in the comment at the beginning of the file.  
  
EDIT: You should also update our copyrights.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2022-02-18 14:41:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/978#issuecomment-1044630999  

There are also 2 additional projections defined in the original/reference file. This is probably out of scope of this PR but we could consisder converting them to BG.

---

## Comment 3

> Username: vissarion  
> Created_at: 2022-03-09 11:02:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/978#issuecomment-1062803712  

@barendgehrels are you ok with merging this PR?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2022-04-08 08:44:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/978#issuecomment-1092609964  

> @barendgehrels are you ok with merging this PR?  
  
Sorry, I had missed this message earlier.

---
