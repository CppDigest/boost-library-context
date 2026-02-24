# #72 add nvcc compiler detection [Merged]

> Username: BenjaminW3  
> Created at: 2018-03-11 17:04:15 UTC  
> Updated at: 2018-05-13 15:43:02 UTC  
> Merged at: 2018-05-13 15:22:00 UTC  
> Closed at: 2018-05-13 15:22:00 UTC  
> Url: https://github.com/boostorg/predef/pull/72  

The version macros `__CUDACC_VER_MAJOR__`, `__CUDACC_VER_MINOR__` and `__CUDACC_VER_BUILD__` have been introduced with CUDA 7.5.

---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2018-03-12 10:15:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/72#pullrequestreview-102983148  

📁 include/boost/predef/compiler/nvcc.h

```diff
  33 |+ #       define BOOST_COMP_NVCC_DETECTION BOOST_VERSION_NUMBER_AVAILABLE
  34 |+ #   else
  35 |+ #       define BOOST_COMP_NVCC_DETECTION BOOST_VERSION_NUMBER(__CUDACC_VER_MAJOR__, __CUDACC_VER_MINOR__, __CUDACC_VER_BUILD__)
```

> Username: ax3l  
> Created_at: 2018-03-12 10:15:53 UTC  
> Updated_at: 2018-03-12 10:18:02 UTC  
> Url: https://github.com/boostorg/predef/pull/72#discussion_r173745970  

I am not an expert on `BOOST_VERSION_NUMBER` but is this save? It looks to me like the numbers are just stitched together (concatination):  
https://github.com/boostorg/predef/blob/822d09f19bc2f4ea6f42da8e0be83d10ce912ce1/test/version.cpp#L32  
  
Released CUDA versions (<=9.1) have currently the formats:  
X.Y.ZZ  
and  
X.Y.ZZZ  
  
and soon (CUDA 10?)  
  
XX.Y.ZZ  
XX.Y.ZZZ ?

> Username: ax3l  
> Created_at: 2018-03-12 10:16:48 UTC  
> Url: https://github.com/boostorg/predef/pull/72#discussion_r173746198  

here is how we stitch it together in boost::config  
https://github.com/boostorg/config/pull/175/files

> Username: BenjaminW3  
> Created_at: 2018-03-12 10:40:16 UTC  
> Url: https://github.com/boostorg/predef/pull/72#discussion_r173752551  

It should be safe. See [here](https://github.com/boostorg/predef/blob/develop/include/boost/predef/version_number.h#L36). The multiplication is nearly the same as the config version but it seems to have one more patch digit.


---

## Comment 2

> Username: ax3l  
> Created_at: 2018-03-13 03:34:14 UTC  
> Url: https://github.com/boostorg/predef/pull/72#issuecomment-372535674  

Yes, but what I wonder is how to declutter the digits again. Afaik, BOOST_VERSION_NUMBER does not zero-pad.  
  
E.g. a BOOST_VERSION_NUMBER(9, 1, 50) > BOOST_VERSION_NUMBER(9, 0, 105) will be false instead of true, due to missing zero paddings.  
  
That's of no good...

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2018-03-13 03:56:15 UTC  
> Url: https://github.com/boostorg/predef/pull/72#issuecomment-372538909  

@ax3l Predef will treat `BOOST_VERSION_NUMBER(9, 1, 50) > BOOST_VERSION_NUMBER(9, 0, 105)` correctly as expected. The version number composition is mathematically based not string based. As described in the docs (http://www.boost.org/doc/libs/release/doc/html/predef/reference.html#predef.reference.version_definition_macros.boost_version_number). Although this assumes that the individual version components are numerically comparable. I.e. that in this example `BOOST_VERSION_NUMBER(9, 1, 50) < BOOST_VERSION_NUMBER(9, 1, 105) == true` -- `50` should be interpreted as fifty and not `500`.

---

## Comment 4

> Username: ax3l  
> Created_at: 2018-03-13 04:32:43 UTC  
> Url: https://github.com/boostorg/predef/pull/72#issuecomment-372543966  

Excellent, thanks!

---

## Comment 5

> Username: ax3l  
> Created_at: 2018-04-05 21:25:12 UTC  
> Url: https://github.com/boostorg/predef/pull/72#issuecomment-379081687  

@BenjaminW3 ping: CI seems to be unhappy

---

## Comment 6

> Username: BenjaminW3  
> Created_at: 2018-04-06 04:56:21 UTC  
> Url: https://github.com/boostorg/predef/pull/72#issuecomment-379147816  

I think CI is unhappy with all PRs at the moment. There seems to be a defective CI script. So nothing related to this PR.

---
