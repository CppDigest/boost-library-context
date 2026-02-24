# #1170 Add proof of concept for NVRTC support [Merged]

> Username: mborland  
> Created at: 2024-08-06 18:14:42 UTC  
> Updated at: 2024-08-08 12:43:07 UTC  
> Merged at: 2024-08-08 12:43:03 UTC  
> Closed at: 2024-08-08 12:43:03 UTC  
> Url: https://github.com/boostorg/math/pull/1170  

Development and completed CUDA runs can be found here: https://github.com/cppalliance/cuda-math/pull/8  
  
This one was requested by @izaid for CuPy usage. To me it seems beneficial to just alias libcu++ anywhere we can for this level of support because NVRTC has extremely strict rules. We're going to have to create aliases to everything in `std::` to either point to `cuda::std::`, `thrust::`, or roll our own implementation for things that don't exist such as the already completed implementation of `<limits>`. I'm also not quite sure how to pass relative paths to `nvrtcCompileProgram` so right now I have everything as hard-coded full paths. This is also for runtime compilation so we get less useful results out of just compilation than we do with NVCC here, but everything is still run first through the cuda-math repo.   
  
Any thoughts: @jzmaddock, @ckormanyos, @NAThompson?

---

## Comment 1

> Username: izaid  
> Created_at: 2024-08-06 18:23:06 UTC  
> Updated_at: 2024-08-06 18:24:04 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2271886862  

Thanks for setting this up @mborland! And ccing @steppi here.  
  
Yes, NVRTC is quite strict, but unfortunately it's used in various places so we would need to support it. We went through this as well, and our solution indeed was just to alias the relevant parts of `std`. It definitely wasn't a complete implementation, but we did copy-and-paste a lot of `<limits>`, etc.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-08-06 19:17:52 UTC  
> Updated_at: 2024-08-06 19:39:59 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2271977920  

@izaid, @steppi, @dschmitz89 Do any of you want or need ROCm support as long as we are going back through this?  
  
edit: To me it doesn't seem especially valuable since there's translations available from SYCL or CUDA code. Looks more like additional maintenance without a ton of benefit.

---

## Comment 3

> Username: izaid  
> Created_at: 2024-08-06 19:43:02 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2272018833  

I don't think we need ROCm support, CUDA is the big win.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-08-06 20:36:08 UTC  
> Updated_at: 2024-08-07 18:32:15 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2272103563  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1170?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.08%. Comparing base [(`ab09ece`)](https://app.codecov.io/gh/boostorg/math/commit/ab09ecec1d4cee65868951aa8dfe5ae4a1e4fa21?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`135208b`)](https://app.codecov.io/gh/boostorg/math/commit/135208b3a642594b7caa3460e8b01441d21456fa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1170/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1170?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1170      +/-   ##  
===========================================  
+ Coverage    94.06%   94.08%   +0.01%       
===========================================  
  Files          780      780                
  Lines        65797    65797                
===========================================  
+ Hits         61892    61904      +12       
+ Misses        3905     3893      -12       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1170?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1170?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `91.75% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1170/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1170?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1170?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ab09ece...135208b](https://app.codecov.io/gh/boostorg/math/pull/1170?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2024-08-07 08:18:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1170#pullrequestreview-2223259813  

📁 include/boost/math/special_functions/gamma.hpp

```diff
  21 |+ 
  22 |+ template <typename T>
  23 |+ __host__ __device__ T tgamma(T x)
```

> Username: jzmaddock  
> Created_at: 2024-08-07 08:18:58 UTC  
> Url: https://github.com/boostorg/math/pull/1170#discussion_r1706587815  

It's somewhat a matter of taste, but I would probably go with:  
  
```  
inline __host__ __device__ float tgamma(float x) { return ::tgammaf(x); }  
inline __host__ __device__ double tgamma(double x) { return ::tgamma(x); }  
```  
  
Certainly it should be inline at least.  I would also add overloads that accept a Policy (and which ignore it), so that when other code calls tgamma it has something to forward to.

> Username: steppi  
> Created_at: 2024-08-07 09:10:34 UTC  
> Url: https://github.com/boostorg/math/pull/1170#discussion_r1706663779  

I think the template functions are automatically inline, but I agree about having separate float and double versions, if that’s what you mean here. Usually you see things like having minimax polynomial expansions which are different for different precision levels. (I know you know this @jzmaddock because I’ve seen this done in the boost math codebase, just pointing it out for everyone else.)


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-08-07 08:20:23 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2272899244  

> We're going to have to create aliases to everything in std:: to either point to cuda::std::, thrust::  
  
Since our calls are all unqualified, can we not just change BOOST_MATH_STD_USING to point to the appropriate namespace?

---

## Comment 7

> Username: steppi  
> Created_at: 2024-08-07 09:05:08 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2272985861  

> Since our calls are all unqualified, can we not just change BOOST_MATH_STD_USING to point to the appropriate namespace?  
  
@izaid and I wanted to do the equivalent of this in SciPy. The hitch was that to get things working in CuPy (which uses NVRTC) we needed a patchwork of different namespaces.  `thrust` for complex arithmetic, mostly `cuda::std`, sometimes just `::` from `cuda_runtime.h`, and I think even had to copy and paste some stuff from the standard library and add the CUDA markup.

---

## Comment 8

> Username: mborland  
> Created_at: 2024-08-07 11:40:52 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2273270080  

> > We're going to have to create aliases to everything in std:: to either point to cuda::std::, thrust::  
>   
> Since our calls are all unqualified, can we not just change BOOST_MATH_STD_USING to point to the appropriate namespace?  
  
The equivalent of `BOOST_MATH_STD_USING` is easy since all the mathematical functions require no special headers and are all in the global namespace. The things we will need from `thrust::` are containers, and we have aliases already like tuple: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/tuple.hpp#L14. Utilities are in `cuda::std::` such as a version of `<type_traits>`, etc.: https://nvidia.github.io/cccl/libcudacxx/standard_api/utility_library/type_traits.html. My thought is to create a bunch of alias headers in `boost/math/tools` like `boost::math::tuple` so if you call say `boost::math::is_same_v` it is either `std::is_same_v` or `cuda::std::is_same_v` depending on context. For posterity these could all be extracted and put into a central place. @jzmaddock what do you think about landing all these kind of context aware aliases in Config? I thought it might be out of scope, but wanted your thoughts. I'd rather not propose a Boost.GPU_Compat library,

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-08-07 11:51:07 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2273286832  

> My thought is to create a bunch of alias headers in boost/math/tools like boost::math::tuple so if you call say boost::math::is_same_v it is either std::is_same_v or cuda::std::is_same_v depending on context. For posterity these could all be extracted and put into a central place. @jzmaddock what do you think about landing all these kind of context aware aliases in Config? I thought it might be out of scope, but wanted your thoughts. I'd rather not propose a Boost.GPU_Compat library,  
  
It's not unreasonable to push this to Config: I'm sure other libraries could make use of this stuff too... but do we want to support this in standalone mode?  It might be worth at least raising this on the mailing list?

---

## Comment 10

> Username: mborland  
> Created_at: 2024-08-07 12:05:37 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2273312405  

> It's not unreasonable to push this to Config: I'm sure other libraries could make use of this stuff too... but do we want to support this in standalone mode? It might be worth at least raising this on the mailing list?  
  
I would keep the original copies here in math so we can continue supporting standalone mode, because that is what the Python packages depend on. I'll post on the ML.

---

## Comment 11

> Username: mborland  
> Created_at: 2024-08-07 15:04:45 UTC  
> Url: https://github.com/boostorg/math/pull/1170#issuecomment-2273694394  

> > It's not unreasonable to push this to Config: I'm sure other libraries could make use of this stuff too... but do we want to support this in standalone mode? It might be worth at least raising this on the mailing list?  
>   
> I would keep the original copies here in math so we can continue supporting standalone mode, because that is what the Python packages depend on. I'll post on the ML.  
  
Looks like the ML is in favor of everything going in it's own library. Pending any other comments I think this is a good limited proof that we can make it work. I'll keep building out the basic functions e.g. `fpclassify` not provided by CUDA and see if we can get a simple distribution working.

---
