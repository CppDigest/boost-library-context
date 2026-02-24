# #21 Added work around for Nvidia nvcc cuda compiler >= 9.0 [Merged]

> Username: theZiz  
> Created at: 2018-04-06 08:33:18 UTC  
> Updated at: 2018-05-02 11:01:20 UTC  
> Merged at: 2018-04-27 17:16:22 UTC  
> Closed at: 2018-04-27 17:16:22 UTC  
> Url: https://github.com/boostorg/mp11/pull/21  

I already opened a[ ticket in the official boost tracker](https://svn.boost.org/trac10/ticket/13513), but thought it is a better idea to directly provide a PR.  
  
While working with cuda and boost::mp11 I noticed that cuda 9 does not work with boost::mp11, even this simple application fails:  
  
```C++  
#include <boost/mp11.hpp>  
int main() {}  
```  
  
This seems to be a bug in the nvidia compiler >= 9, not boost, but this is a showstopper for using boost::mp11 together with a recent cuda version. Everything works fine with cuda 8. I didn't test an earlier version, but there is no C++11 before cuda 7 anyway.  
  
I already figured out, what fails in the background in the nvcc (details are [​here](https://github.com/ComputationalRadiationPhysics/alpaka/issues/459#issuecomment-377678240)), but now also found a work around for boost::mp11:  
  
The problem occurs on code like this:  
  
```C++  
template<template<class...> class F, class... T> using mp_defer = mp_if<mp_valid<F, T...>, detail::mp_defer_impl<F, T...>, detail::mp_no_type>;  
```  
  
Everytime `mp_if` is used, the `nvcc` preprocessor tries to be "smart" and starts recursively replacing stuff so that in the end `detail::mp_no_type...` is given to the compiler (please don't ask _why_, some details are in the link above).  
  
I figured out I can prevent `nvcc` from doing this with invoking another `struct` inbetween like this:  
  
```C++  
template<template<class...> class F, class... T>  
struct cuda_workaround  
{  
    using type = mp_if<mp_valid<F, T...>, detail::mp_defer_impl<F, T...>, detail::mp_no_type>;  
};  
  
template<template<class...> class F, class... T>  
using mp_defer = typename cuda_workaround< F, T... >::type;  
```  
  
This work around is needed on 5 places in the boost mp11 (see commit).  
  
I added an intermediate struct in the detail namespace as seen above for every case if cuda 9 is used and now my code compiles with `nvcc` 9.  
  
If the commit / PR needs adjustments to suit your style requirements, don't hesitate to ask. ;)

---

## Comment 1

> Username: ax3l  
> Created_at: 2018-04-26 09:33:40 UTC  
> Updated_at: 2018-04-26 09:33:46 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384576692  

@pdimov this is another CUDA issue, this time with CUDA 9.0 and nvcc.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-04-26 10:51:25 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384596775  

I know. We're not done with the other one yet.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-04-26 11:09:56 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384601588  

As far as I can see, the nvcc on Travis is version 5.5, so there's no way to test this, and I might as well apply it.  
  
However, we don't usually use `>=` workarounds. This implies that all versions from 9.0 onwards will always remain broken, and our general assumption is that things will be fixed at some point. In this case we'd probably just use the workaround for version 9 and assume that it will be fixed in 10 and above.

---

## Comment 4

> Username: ax3l  
> Created_at: 2018-04-26 14:57:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384671589  

> Travis  
  
I would say when we have integrated the tests in the other PR, we can add another test doing `nvcc -I . -std=c++11 libs/mp11/test/mp11.cpp` for this one.  
  
> we don't usually use >= workarounds  
  
@theZiz does your cuda developer access work again? If you post the issue there (make sure to point them to the boost 1.66 release) they can fix it hopefully as well so you can change the `<=` to a ~9.X match?

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-04-26 15:14:16 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384678190  

We'd need to install nvcc 9 though, not nvcc 8, as you seem to do in that other PR.

---

## Comment 6

> Username: ax3l  
> Created_at: 2018-04-26 15:46:14 UTC  
> Updated_at: 2018-04-26 16:04:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384690708  

Yep, that's what I meant.  
  
We can just add the same logic for another cuda release and can even add `nvcc` 8.0 to the other one when we are here, just for additional coverage with the same run.  
  
https://github.com/ax3l/mp11/commit/fa1923539488d6dd7b027ace852a7f3b1a928109 ([travis](https://travis-ci.org/ax3l/mp11/builds/371637219))

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-04-26 16:38:49 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384708387  

Am I correct that installing the 9.1 SDK requires a 1.6G download?

---

## Comment 8

> Username: theZiz  
> Created_at: 2018-04-26 16:49:44 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384711639  

@pdimov Unfortunately this makes sense, a trustworthy big binary blob of Nvidia  
  
Regarding, the `>=` work around, it makes sense, I will change this.  
  
@ax3l No, neither the developer access works nor received any of my mails an answer. Feel free to submit the bug report yourself if the nvidia developer website is still working for you.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-04-26 16:58:08 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384714233  

I don't mind binary blobs on Travis, but this takes 15 minutes for me, who knows how much there. Oh well.

---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2018-04-26 17:01:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/21#pullrequestreview-115665343  

📁 include/boost/mp11/algorithm.hpp

```diff
  96 | };
  97 | 
  98 |+ #if BOOST_WORKAROUND( BOOST_CUDA_VERSION, >= 9000000 ) && BOOST_WORKAROUND( BOOST_CUDA_VERSION, < 10000000 )
```

> Username: pdimov  
> Created_at: 2018-04-26 17:01:32 UTC  
> Updated_at: 2018-04-27 06:54:38 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#discussion_r184462653  

We usually use  
  
BOOST_WORKAROUND( BOOST_CUDA_VERSION, >= 9000000 && BOOST_CUDA_VERSION < 10000000 )  
  
for those (the two macro arguments are concatenated so you could use an expression.)  
  
There's also the cuter  
  
BOOST_WORKAROUND( BOOST_CUDA_VERSION, / 1000000 == 9 )  
  
although this doesn't update well to a range of versions (in case 10 also needs the fix.)

> Username: theZiz  
> Created_at: 2018-04-26 17:10:03 UTC  
> Updated_at: 2018-04-27 06:54:38 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#discussion_r184464916  

Oh, okay, I was not sure how `BOOST_WORKAROUND` was exactly working in the background. :) I will go for the second version as we need to adjust this anyway if nvidia doesn't fix this bug for version 10. :wink:


---

## Comment 11

> Username: pdimov  
> Created_at: 2018-04-26 18:21:48 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384741224  

The application of the other PR has introduced a merge conflict here; once you resolve that, this looks good to go.

---

## Comment 12

> Username: theZiz  
> Created_at: 2018-04-26 18:26:34 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384743050  

Okay, le me just fix this

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-04-26 22:03:47 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384803095  

I applied this fix to branch `feature/nvcc-9`, but it still fails: https://travis-ci.org/boostorg/mp11/jobs/371704635

---

## Comment 14

> Username: theZiz  
> Created_at: 2018-04-27 06:37:21 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384878992  

`./boost/mp11/detail/mp_append.hpp:150:223: error: expected class-name before ‘template’`← this line is not part of the work around but the default `#else` case, so the work around check is not working for some reason. :confused: I will check this.

---

## Comment 15

> Username: theZiz  
> Created_at: 2018-04-27 06:51:07 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-384881578  

Okay, I know why this failed. `BOOST_CUDA_VERSION` is defined as `#  define BOOST_CUDA_VERSION __CUDACC_VER_MAJOR__ * 1000000 + __CUDACC_VER_MINOR__ * 10000 + __CUDACC_VER_BUILD__` without any brackets. :roll_eyes: I will change the check for the version for the other suggested alternative. :wink:

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-04-27 18:11:07 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-385050843  

OK, works and merged. Will you submit the obvious PR against Boost.Config, or should I?

---

## Comment 17

> Username: theZiz  
> Created_at: 2018-04-27 18:13:47 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-385051609  

Thanks, I will open a PR for boost.config, not the first times I need changes. :wink:

---

## Comment 18

> Username: pdimov  
> Created_at: 2018-04-28 17:33:40 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-385192809  

https://github.com/boostorg/config/pull/224

---

## Comment 19

> Username: ax3l  
> Created_at: 2018-04-30 08:56:43 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-385343875  

thanks for the config clean-up!

---

## Comment 20

> Username: theZiz  
> Created_at: 2018-05-02 11:01:20 UTC  
> Url: https://github.com/boostorg/mp11/pull/21#issuecomment-385940564  

Thanks @pdimov!

---
