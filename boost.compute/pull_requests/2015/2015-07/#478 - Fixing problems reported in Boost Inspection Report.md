# #478 Fixing problems reported in Boost Inspection Report [Closed]

> Username: jszuppe  
> Created at: 2015-07-16 20:24:11 UTC  
> Updated at: 2015-07-18 10:37:51 UTC  
> Closed at: 2015-07-18 10:37:51 UTC  
> Url: https://github.com/boostorg/compute/pull/478  

I fixed some of problems reported in [Boost Inspection Report for 1.59 beta rc1](http://boost.cowic.de/rc/docs-inspect-develop.html#compute). I could not fix all of them due to problems/issues/doubts I described below.  
  
I fixed violations of Boost min/max guidelines, converted tabs into spaces, added missing license information and copyrights (based on who and when created the file). I'm not sure how to correctly add missing license and copyright info to .qbk files.   
  
I fixed one non-ASCII character in `/example/opencv_convolution.cpp`, but I did not change names with non-ASCII character/s as I don't think it would be right.  
  
A few of Boost marcos used in Boost.Compute are deprecated, some since 1.50, some since 1.51. Boost.Compute requires Boost in version 1.48 or later. New macros that replaced deprecated ones (see [Boost macros](http://www.boost.org/doc/libs/1_58_0/libs/config/doc/html/boost_config/boost_macro_reference.html)) are - of course - not present in 1.48, so if Boost.Compute should work with 1.48 we need to keep these deprecated macros. Otherwise, required Boost version should be set to 1.51.   
  
( I also check that in `compute/include/boost/compute/config.hpp` internal macros like BOOST_COMPUTE_DETAIL_NO_VARIADIC_TEMPLATES are defined, but still sometimes in the code original macros from Boost are used, so that should be fixed too? )

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-07-18 06:09:02 UTC  
> Url: https://github.com/boostorg/compute/pull/478#issuecomment-122489151  

Thanks for fixing all these! Could you make this pull request against the `develop` branch?

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-07-18 07:31:14 UTC  
> Url: https://github.com/boostorg/compute/pull/478#issuecomment-122508746  

Oh, sorry for this mistake :-) I'll add more fixes today and make pull  
request against develop branch.  
  
Could you comment doubts/problems I listed?  
18 lip 2015 08:09 "Kyle Lutz" notifications@github.com napisał(a):  
  
> Thanks for fixing all these! Could you make this pull request against the  
> develop branch?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/pull/478#issuecomment-122489151.

---
