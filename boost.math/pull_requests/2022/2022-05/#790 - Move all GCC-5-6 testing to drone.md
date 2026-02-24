# #790 Move all GCC-5/6 testing to drone. [Merged]

> Username: jzmaddock  
> Created at: 2022-05-31 17:17:19 UTC  
> Updated at: 2024-01-24 21:35:08 UTC  
> Merged at: 2022-06-04 08:19:53 UTC  
> Closed at: 2022-06-04 08:19:53 UTC  
> Url: https://github.com/boostorg/math/pull/790  

Remove C++11 testing for gcc where the compiler default is C++14.  
Remove C++11 testing from msvc - it doesn't support it, only C++14.  
All C++11 testing is now on gcc-5 and 6 and on drone.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-06-04 08:19:47 UTC  
> Url: https://github.com/boostorg/math/pull/790#issuecomment-1146566319  

One failure is unrelated to us (in Boost.Hash, fix in progress) so merging.  
  
@mborland : this moves about one half of the testing load to drone, so we'll see if this speeds up the CI pipeline a bit.

---
