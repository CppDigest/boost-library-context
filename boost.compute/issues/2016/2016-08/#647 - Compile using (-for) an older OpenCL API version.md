# #647 - Compile using (/for) an older OpenCL API version [Closed]

> Username: amirshavit  
> Created at: 2016-08-22 18:14:57 UTC  
> Updated at: 2017-04-24 16:07:45 UTC  
> Closed at: 2017-04-24 16:07:45 UTC  
> Url: https://github.com/boostorg/compute/issues/647  

Currently, if the cl.h header supports newer API versions (say CL_VERSION_2_1), boost::compute uncontrollably uses that API version. If I run my compiled binary on a different system, with an older opencl library, the binary will fail to load due to missing exports in the dll/so.  
  
I see there are run-time version checks (`device.check_version(...)`) but they aren't helpful for this problem.  
  
An easy solution could be if the cl.hpp (of boost::compute) could #undef those (after `#include <.../cl.h>`), something like -  
  
``` c++  
#if defined(BOOST_COMPUTE_AVOID_CL_VERSION_2_0) && defined(CL_VERSION_2_0)  
#undef CL_VERSION_2_0  
#endif  
...  
```  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-08-22 18:20:44 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-241502950  

Yeah, IIRC that was mentioned a few times. Basically, you postulate to add macros for selecting the newest supported OpenCL version instead of using the newest defined in used `cl.h`, yes?

---

## Comment 2

> Username: amirshavit  
> Created at: 2016-08-22 18:35:53 UTC  
> Updated at: 2016-08-22 18:53:33 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-241507489  

Yes.  
  
An alternative form (based on your suggestion) -  
  
``` c++  
#if defined(BOOST_COMPUTE_MAX_CL_VERSION)  
// ...  
#  if BOOST_COMPUTE_MAX_CL_VERSION < 200 && defined(CL_VERSION_2_0)  
#    undef CL_VERSION_2_0  
#  endif  
// ...  
#endif  
```  
  
I'm not sure if `#undef`-ing is the so-called right solution, but it sure is the fastest.

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-08-22 18:37:51 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-241508035  

I think the right way would be to define internal macros based on OpenCL version macros and options set by a user, and replace OpenCL version marcos (like `CL_VERSION_2_0`) with them.

---

## Comment 4

> Username: amirshavit  
> Created at: 2016-08-22 18:40:50 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-241508949  

I agree.  Should I open a pull request or is this something for further consideration?

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-08-22 18:45:04 UTC  
> Updated at: 2016-08-22 18:45:27 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-241510144  

If you want to do this, then do this (props for you) and open a pull request and wait for @kylelutz to accept it. If you don't have time for this, then you have to wait for me or @kylelutz to code this, but I don't have time to do that this month.

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-04-24 16:07:45 UTC  
> Url: https://github.com/boostorg/compute/issues/647#issuecomment-296722055  

Fixed - https://github.com/boostorg/compute/pull/699.
