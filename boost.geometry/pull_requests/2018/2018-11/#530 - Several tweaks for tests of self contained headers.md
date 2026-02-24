# #530 Several tweaks for tests of self contained headers. [Merged]

> Username: awulkiew  
> Created at: 2018-11-27 20:05:16 UTC  
> Updated at: 2018-11-27 21:18:37 UTC  
> Merged at: 2018-11-27 21:16:58 UTC  
> Closed at: 2018-11-27 21:16:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/530  

Shorter paths.  
was:  
  
    bin.v2\libs\geometry\test\self_contained_headers\self-contained-core-access~hpp.test\msvc-14.1\dbg\async-excpt-on\thrd-mlt\self-contained-core-access~hpp.obj  
  
is:  
  
    bin.v2\libs\geometry\test\headers\h-core-access.test\msvc-14.1\dbg\async-excpt-on\thrd-mlt\h-core-access.obj  
  
Extensions headers tested in `extensions/test` dir.

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-11-27 20:07:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/530#issuecomment-442198845  

I have not hit the long paths issue myself, so never considered this needs fixing. Thanks.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-11-27 20:31:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/530#issuecomment-442206317  

It's more pleasant to read too. I also duplicated the Jamfile and moved the extensions bit to `extensions/test` since they are not present in master branch.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2018-11-27 20:35:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/530#issuecomment-442207662  

Thanks, OK for me

---
