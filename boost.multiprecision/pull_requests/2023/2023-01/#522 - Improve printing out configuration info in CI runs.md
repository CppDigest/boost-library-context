# #522 Improve printing out configuration info in CI runs. [Merged]

> Username: jzmaddock  
> Created at: 2023-01-17 17:05:17 UTC  
> Updated at: 2023-01-19 17:25:00 UTC  
> Merged at: 2023-01-19 17:25:00 UTC  
> Closed at: 2023-01-19 17:25:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/522  

@ckormanyos This PR should (hopefully) improve the configuration info that's printed out with each CI run, including the b2 configuration logs.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-01-17 18:19:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/522#issuecomment-1385840483  

> PR should (hopefully) improve the configuration info that's printed out with each CI run  
  
Thanks John! That's going to be great when the kinks get ironed out.  
  
If we like it and it's stable, we could consider `grep`-ing, `gawk`-ing or `sed`-ing, for even more summaries. I could look into that if we decide there might be (even more) added value to gain...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-01-17 18:47:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/522#issuecomment-1385877796  

Hopefully small kinks, some useful info though like:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/multiprecision/config/gcc-10/debug/threading-multi/visibility-hidden/has_gmp.o  
In file included from ../../../boost/config/detail/suffix.hpp:572,  
                 from ../../../boost/config.hpp:61,  
                 from ../config/has_gmp.cpp:8:  
../config/has_gmp.cpp:11:69: note: ‘#pragma message: __GNU_MP_VERSION=6’  
   11 | #pragma message "__GNU_MP_VERSION=" BOOST_STRINGIZE(__GNU_MP_VERSION)  
      |                                                                     ^  
../config/has_gmp.cpp:12:81: note: ‘#pragma message: __GNU_MP_VERSION_MINOR=2’  
   12 | #pragma message "__GNU_MP_VERSION_MINOR=" BOOST_STRINGIZE(__GNU_MP_VERSION_MINOR)  
      |                                                                                 ^  
```  
  
That could probably be made neater by printing it all out from a config_info like program, but it'll do for now.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-01-18 18:23:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/522#issuecomment-1387516732  

> it'll do for now  
  
Yes. This is great. Thank you for this quick and reliable improvement.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-01-19 17:24:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/522#issuecomment-1397342819  

Failure is spurious, merging.

---
