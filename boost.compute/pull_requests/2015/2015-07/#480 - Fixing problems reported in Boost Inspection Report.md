# #480 Fixing problems reported in Boost Inspection Report [Merged]

> Username: jszuppe  
> Created at: 2015-07-18 14:19:45 UTC  
> Updated at: 2016-05-01 11:21:04 UTC  
> Merged at: 2015-07-18 18:01:57 UTC  
> Closed at: 2015-07-18 18:01:57 UTC  
> Url: https://github.com/boostorg/compute/pull/480  

I fixed some of problems reported in [Boost Inspection Report for 1.59 beta rc1](http://boost.cowic.de/rc/docs-inspect-develop.html#compute).   
- I fixed violations of Boost min/max guidelines, converted tabs into spaces, added missing license information and copyrights (based on who and when created the file).  
- I fixed one non-ASCII character in `/example/opencv_convolution.cpp`, but I _DID NOT_ change author name with non-ASCII character/s as I don't think it would be right.  
- I added `BOOST_NO_CXX11_VARIADIC_TEMPLATES` macro which replaces deprecated `BOOST_NO_VARIADIC_TEMPLATES`.  
- I also moved all `BOOST_NO_*` to `config.hpp`.   
- I used `BOOST_COMPUTE_NO_` prefix for all these macros, but it's not a problem to changed the prefix if different is preferred (for example  `BOOST_COMPUTE_DETAIL_NO_`).  
- (!) The last commit removes deprecated Boost macros and update required version of Boost Library to 1.55 (in Boost 1.48 new macros that replaces deprecated ones are not defined, theoretically 1.51 is enough, but IMO 1.55 is currently the most popular one + there are no packages for 1.51). This commit can be removed if we want to keep supporting 1.48 version.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-07-18 16:27:34 UTC  
> Url: https://github.com/boostorg/compute/pull/480#issuecomment-122561485  

Looks good! As for your last comment, I think we still want to keep support for Boost 1.48 (at least until Boost.Compute is in the official Boost release).

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-07-18 17:12:17 UTC  
> Url: https://github.com/boostorg/compute/pull/480#issuecomment-122566564  

OK, so I will remove last commit and it will be ready to merge.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-07-18 17:50:50 UTC  
> Url: https://github.com/boostorg/compute/pull/480#issuecomment-122571522  

Done, last commit is removed. Deprecated macros will still generate problems in Boost inspection program, but we keep support for 1.48 version. My commit will wait patiently ;)

---
