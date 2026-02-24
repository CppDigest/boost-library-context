# #175 Update for CUDA version macro changes. [Merged]

> Username: jzmaddock  
> Created at: 2017-08-11 18:38:12 UTC  
> Updated at: 2019-11-02 11:17:18 UTC  
> Merged at: 2017-08-22 17:55:15 UTC  
> Closed at: 2017-08-22 17:55:15 UTC  
> Url: https://github.com/boostorg/config/pull/175  



---

## Review 1 [Commented]

> Username: ax3l  
> Created_at: 2017-08-14 08:05:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/175#pullrequestreview-56015821  

I would crop the `*_BUILD__` to two significant digits from the left

📁 include/boost/config/compiler/nvcc.hpp

```diff
  13 | 
  14 |+ #if defined(__CUDACC_VER_MAJOR__) && defined(__CUDACC_VER_MINOR__) && defined(__CUDACC_VER_BUILD__)
  15 |+ #  define BOOST_CUDA_VERSION __CUDACC_VER_MAJOR__ * 10000 + __CUDACC_VER_MINOR__ * 100 + __CUDACC_VER_BUILD__
```

> Username: ax3l  
> Created_at: 2017-08-14 08:02:57 UTC  
> Updated_at: 2017-08-18 17:13:39 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r132892843  

@jzmaddock careful, even CUDA 9 RC2 is already over 100 builds in `__CUDACC_VER_BUILD__`.  
  
In my bug report I suggested to either increase for CUDA 9 build by a full order (*10) or to crop the build to two significant ints from the left during deprecation.

---

📁 include/boost/config/compiler/nvcc.hpp

```diff
  14 |+ #if defined(__CUDACC_VER_MAJOR__) && defined(__CUDACC_VER_MINOR__) && defined(__CUDACC_VER_BUILD__)
  15 |+ #  define BOOST_CUDA_VERSION __CUDACC_VER_MAJOR__ * 10000 + __CUDACC_VER_MINOR__ * 100 + __CUDACC_VER_BUILD__
  16 |+ #elif defined(__CUDACC_VER__)
```

> Username: ax3l  
> Created_at: 2017-08-14 08:04:54 UTC  
> Updated_at: 2017-08-18 17:13:39 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r132893153  

note: at least in CUDA 9 RC2 this var will be defined and `__CUDACC_VER__` *is* set to a string with an error message.  
  
since the other 3 are defined above, this `elif` should not be taken but it's maybe still a bit risky.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-08-14 10:56:18 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-322161117  

On 14/08/2017 09:05, Axel Huebl wrote:  
>  
> *@ax3l* commented on this pull request.  
>  
> I would crop the |*_BUILD__| to two significant digits from the left  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/config/compiler/nvcc.hpp   
> <https://github.com/boostorg/config/pull/175#discussion_r132892843>:  
>  
> > @@ -11,6 +11,15 @@  
>   #  define BOOST_COMPILER "NVIDIA CUDA C++ Compiler"  
>   #endif  
>     
> +#if defined(__CUDACC_VER_MAJOR__) && defined(__CUDACC_VER_MINOR__) && defined(__CUDACC_VER_BUILD__)  
> +#  define BOOST_CUDA_VERSION __CUDACC_VER_MAJOR__ * 10000 + __CUDACC_VER_MINOR__ * 100 + __CUDACC_VER_BUILD__  
>  
> @jzmaddock <https://github.com/jzmaddock> careful, even CUDA 9 RC2 is   
> already above 100 builds in |__CUDACC_VER_BUILD__|.  
>  
> In my bug report I suggested to either increase for CUDA 9 build by a   
> full order (*10) or to crop the build to two significant ints from the   
> left during deprecation.  
>  
  
OK, or is there any reason to include the build number at all - perhaps   
we should just include the major and minor version numbers?  
  
> ------------------------------------------------------------------------  
>  
> In include/boost/config/compiler/nvcc.hpp   
> <https://github.com/boostorg/config/pull/175#discussion_r132893153>:  
>  
> > @@ -11,6 +11,15 @@  
>   #  define BOOST_COMPILER "NVIDIA CUDA C++ Compiler"  
>   #endif  
>     
> +#if defined(__CUDACC_VER_MAJOR__) && defined(__CUDACC_VER_MINOR__) && defined(__CUDACC_VER_BUILD__)  
> +#  define BOOST_CUDA_VERSION __CUDACC_VER_MAJOR__ * 10000 + __CUDACC_VER_MINOR__ * 100 + __CUDACC_VER_BUILD__  
> +#elif defined(__CUDACC_VER__)  
>  
> note: at least in CUDA 9 RC2 this var will be defined and   
> |__CUDACC_VER__| /is/ set to a string with an error message.  
>  
> since the other 3 are defined above, this |elif| should not be taken   
> but it's maybe still a bit risky.  
>  
  
I guess if __CUDACC_VER__ and __CUDACC_VER_MAJOR__ were introduced at   
the same time that branch may be redundant anyway.  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: ax3l  
> Created_at: 2017-08-14 11:10:20 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-322163551  

> OK, or is there any reason to include the build number at all - perhaps we should just include the major and minor version numbers?  
  
Yes we should, CUDA 8.0 got after the initial public release (8.0.44) a bugfix release (8.0.61) and things can change between those. Even if we are testing RC's like now it's not too bad to have this information, but let us crop it two two digits from the left (with Boost.PP?), public releases until now did easily more than 10 builds in between.  
  
> I guess if `__CUDACC_VER__` and `__CUDACC_VER_MAJOR__` were introduced at the same time that branch may be redundant anyway.  
  
I think they were.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-08-18 17:16:24 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-323410766  

I've updated the patch to allow the build number to go up to 9999, there should still be room for the major version to go up to 1000 without overflow.  I think that should provide us with enough headroom for a while at least?

---

## Comment 5

> Username: ax3l  
> Created_at: 2017-08-21 07:58:43 UTC  
> Updated_at: 2017-08-21 07:59:25 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-323673589  

I think that's enough safety margin ;)  
now I am more afraid of us miscounting the zeros.

---

## Review 6 [Commented]

> Username: ax3l  
> Created_at: 2017-08-21 08:03:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/175#pullrequestreview-57425945  

typo in 7.5

📁 include/boost/config/compiler/nvcc.hpp

```diff
  28 | // check is enough to detect versions < 7.5
  22 |- #if !defined(__CUDACC_VER__) || (__CUDACC_VER__ < 70500)
  29 |+ #if BOOST_CUDA_VERSION < 7050000
```

> Username: ax3l  
> Created_at: 2017-08-21 08:02:34 UTC  
> Updated_at: 2017-08-21 08:04:16 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r134166832  

I think `7.5` would be `7005000` now, no?

> Username: ax3l  
> Created_at: 2017-08-21 08:04:10 UTC  
> Updated_at: 2017-08-21 08:04:52 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r134167131  

ah no, did not see it properly. It's fine

---

📁 include/boost/config/compiler/nvcc.hpp

```diff
  32 | // The same bug is back again in 8.0:
  26 |- #if (__CUDACC_VER__ > 80000) && (__CUDACC_VER__ < 80100)
  33 |+ #if (BOOST_CUDA_VERSION > 8000000) && (BOOST_CUDA_VERSION < 8010000)
```

> Username: ax3l  
> Created_at: 2017-08-21 08:02:59 UTC  
> Updated_at: 2017-08-21 08:05:52 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r134166898  

For readability, I think this can be `BOOST_CUDA_VERSION > 8000000` and `< 9000000`  
(there was no CUDA 8.5 which this tried to keep it open for)

---

📁 include/boost/config/compiler/nvcc.hpp

```diff
  52 | //
  46 |- #if (__CUDACC_VER__ >= 80000) && (__CUDACC_VER__ < 80100)
  53 |+ #if (BOOST_CUDA_VERSION >= 8000000) && (BOOST_CUDA_VERSION < 8010000)
```

> Username: ax3l  
> Created_at: 2017-08-21 08:03:08 UTC  
> Updated_at: 2017-08-21 08:03:18 UTC  
> Url: https://github.com/boostorg/config/pull/175#discussion_r134166921  

same here


---

## Review 7 [Approved]

> Username: ax3l  
> Created_at: 2017-08-21 08:04:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/config/pull/175#pullrequestreview-57426289  

Ah, it's all right.  
I thought we go for.  
major 3 digits minor 3 digits build

---

## Comment 8

> Username: BenjaminW3  
> Created_at: 2017-08-28 06:01:19 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-325267132  

@jzmaddock @ax3l Could this fix please be added to the upcoming boost-1.65.1 release see [here](http://boost.2283326.n4.nabble.com/release-Starting-1-65-1-td4698309.html)?

---

## Comment 9

> Username: cdeterman  
> Created_at: 2018-02-09 16:10:46 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-364478296  

Has this been added to the current boost version or part of boost 1.66?

---

## Comment 10

> Username: ax3l  
> Created_at: 2018-02-09 16:12:14 UTC  
> Updated_at: 2018-02-09 16:13:55 UTC  
> Url: https://github.com/boostorg/config/pull/175#issuecomment-364478721  

Yes, this is part of boost 1.65.1 and 1.66.0 (see tags in merge-commit a7dd324d41473830d1750ce39a0415a787db824a)

---
