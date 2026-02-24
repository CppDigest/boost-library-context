# #616 Improvements of srs transformations and shapefile loading. [Merged]

> Username: awulkiew  
> Created at: 2019-08-21 17:29:43 UTC  
> Updated at: 2019-10-25 12:16:03 UTC  
> Merged at: 2019-10-25 12:16:03 UTC  
> Closed at: 2019-10-25 12:16:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/616  

transformations:  
- Introduced shared_grid concept and separated synchronization primitives from algorithms  
- 2 separate shared grids implementations, one depending on Boost.Thread and the other one on STD. Not included automatically, the user picks the one that is needed.  
- Algorithms of shared grids moved from different files to the one containing the implementation for regular grids.  
- Low level projections are implemented differently, no CRTP, parameters moved outside to wrappers. This allows to share the parameters by multiple projections and to remove some virtual calls from dynamic projection.  
- Some memory optimizations:  
  - separating proj_params for ellipsoidal and spherical versions in case where only a subset of parameters is used (sinu)  
  - sharing the parameters between multiple projections (goode, igh)  
  - rearranging parameters WRT allignment  
  
shapefile:  
- Added special handling of loading of doubles which should work with the majority of architectures. The purpose is also to see which Boost runners will fail.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-09-17 21:18:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-532406307  

Btw, I added you @mloskot as a reviewer because I'd like to hear you opinion specifically about the automatic handling of endianness of doubles (https://github.com/boostorg/geometry/pull/616/files#diff-bc4368b6a55393894fde6f1848242794).

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-09-18 09:23:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-532601020  

@awulkiew   
> // It is also not clear why did the generic version above compile in the   
> // past.  
  
Because it has never been used on anything else but either on little or big endian architectures?  
Because nobody compiled it on ARMs with half-s?

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2019-09-18 09:51:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/616#pullrequestreview-289792920  

That's a nice improvement! I am ok with merging.

📁 include/boost/geometry/srs/projections/impl/base_static.hpp

```diff
 157 |         }
 125 |-         catch(...)
 158 |+         catch (...)
```

> Username: vissarion  
> Created_at: 2019-09-18 09:40:58 UTC  
> Updated_at: 2019-09-19 15:09:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#discussion_r325579365  

is this change by intention? it seems that `catch(...)` is used more than `catch (...)` in the library

> Username: awulkiew  
> Created_at: 2019-09-18 13:22:03 UTC  
> Updated_at: 2019-09-19 15:09:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#discussion_r325671128  

Yes, it seems that it's mixed in the library. In SRS the version with space is used more often. But it doesn't matter because we should probably use `BOOST_CATCH` instead anyway. I propose to change it in a separate PR in the whole library at once.


---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-09-18 13:50:31 UTC  
> Updated_at: 2019-09-18 16:01:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-532693484  

@mloskot  
There were no test files for `read_shapefile`.  
There were no special handling of doubles before this PR.  
  
Boost.Endian was always used for conversion between endians in `read_shapefile` code. Boost.Endian checks the type of variable at compile time and it fails to compile for floating point types. So the original code of `read_shapefile` written before should've failed to compile back then when I was testing it locally as it did when I got back to it recently.  
  
AFAIU the checks were added to Boost.Endian fairly recently:  
https://github.com/boostorg/endian/commit/f0f8e398d67adb25314cda47dabb64c964adb005  
so the reason is probably that I used older version of this library while testing `read_shapefile` locally.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-09-19 08:54:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-533035284  

@awulkiew I think your explanation makes sense and the Boost.Endian update with new checks may be the reason behind the different compilation results. To be honest, I have not monitored Boost.Endian for a very long time.

---

## Review 6 [Commented]

> Username: mloskot  
> Created_at: 2019-09-19 15:52:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/616#pullrequestreview-290671112  

📁 include/boost/geometry/extensions/gis/io/shapefile/read.hpp

```diff
 115 | //   them may use mixed endianness (half little-endian, half big-endian) for
 116 | //   doubles or different endianness than the one used for integers.
 117 |- //   It is also not clear why did the generic version above compile in the
```

> Username: mloskot  
> Created_at: 2019-09-19 15:52:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#discussion_r326252995  

Just for the history archive: it's been obsolete as per https://github.com/boostorg/geometry/pull/616#issuecomment-532693484


---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-09-19 18:03:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-533243861  

@mloskot I also noticed that in WKB code are endian-related defines used that are deprecated now. So I think I'll attempt to unify the endian-related code from WKB and shapefile. Not in this PR. The shapefile change should probably not be a part of this PR in the first place.  
  
@barendgehrels what do you think about the change in projections? In particular about replacing CRTP with wrapper?

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-09-19 18:34:34 UTC  
> Updated_at: 2019-09-19 18:35:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-533255716  

@awulkiew I vaguely remember I worked with the Boost.Endian-based code while adding the WKB support. I'm not sure why the git history is lacking of those changes, but here are some archived references:  
- [[ggl] [Ticket #2] Implementation of io/wkb module](https://lists.boost.org/geometry/2009/06/0275.php)  
- [[ggl] Added boost/ggl/util/endian.hpp](https://lists.boost.org/geometry/2009/06/0272.php)  
  
So, I stole the code from somewhere else, the Spirit and others.  
  
A unification sounds like the right thing to do.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2019-09-19 19:26:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-533273585  

@mloskot Damn, 10 years ago. Thanks for the info.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2019-09-23 17:32:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-534202902  

Thanks for the work!   
I still didn't review it properly, but it is very much appreciated

---

## Comment 11

> Username: awulkiew  
> Created_at: 2019-10-19 15:08:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/616#issuecomment-544157406  

@barendgehrels do you plan to review it fully or are you satisfied with the quick one you made?

---
