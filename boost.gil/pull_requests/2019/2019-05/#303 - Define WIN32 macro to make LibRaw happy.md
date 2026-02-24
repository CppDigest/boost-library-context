# #303 Define WIN32 macro to make LibRaw happy [Merged]

> Username: mloskot  
> Created at: 2019-05-31 19:56:39 UTC  
> Updated at: 2019-05-31 20:42:14 UTC  
> Merged at: 2019-05-31 20:41:23 UTC  
> Closed at: 2019-05-31 20:41:23 UTC  
> Url: https://github.com/boostorg/gil/pull/303  

Apparently, for some not entirely clear reasons, possibly historical use of `.vcproj`, LibRaw uses (not defines!) `WIN32` macro, instead of C standard compliant `_WIN32` common to majority of compilers on Windows.  
  
This patch is required if GIL IO is used with Boost.Build or custom Makefile, without this custom `WIN32` hand-rolled in build configuration.  
  
Hopefully, LibRaw may accept some clean up for this in near future: https://github.com/LibRaw/LibRaw/pull/206  
  
### Tasklist  
  
- [x] Ensure all CI builds pass

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-05-31 20:41:12 UTC  
> Updated_at: 2019-05-31 20:42:14 UTC  
> Url: https://github.com/boostorg/gil/pull/303#issuecomment-497853256  

The change is trivial and the large selection of builds is fine, no point waiting hours for Travis to start for `boostorg/gil`  
- https://travis-ci.org/mloskot/gil/builds/539864195  
- https://dev.azure.com/boostorg/gil/_build/results?buildId=422  
- https://ci.appveyor.com/project/stefanseefeld/gil/builds/24960160  
It's been tested on Linux locally too.

---
