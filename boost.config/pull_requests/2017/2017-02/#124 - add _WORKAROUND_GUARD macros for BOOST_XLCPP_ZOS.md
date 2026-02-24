# #124 add _WORKAROUND_GUARD macros for BOOST_XLCPP_ZOS [Merged]

> Username: pgroke-dt  
> Created at: 2017-02-28 12:02:37 UTC  
> Updated at: 2017-03-03 18:32:07 UTC  
> Merged at: 2017-03-03 18:25:16 UTC  
> Closed at: 2017-03-03 18:25:16 UTC  
> Url: https://github.com/boostorg/config/pull/124  

I'd missed workaround.hpp. And since `__IBMCPP__` doesn't uniquely identify the z/OS compiler, I guess there should be a `_WORKAROUND_GUARD` for `BOOST_XLCPP_ZOS`...  
  
Is there anything else that needs to be done for `BOOST_WORKAROUND()` support?

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-03-03 18:25:12 UTC  
> Url: https://github.com/boostorg/config/pull/124#issuecomment-284031368  

This won't do any harm - the _WORKAROUND_GUARD thing just suppresses warnings that may be thrown out by the preprocessor otherwise.  Unless BOOST_WORKAROUND is actually being used with BOOST_XLCPP_ZOS though it isn't really required either.

---

## Comment 2

> Username: pgroke-dt  
> Created_at: 2017-03-03 18:31:45 UTC  
> Url: https://github.com/boostorg/config/pull/124#issuecomment-284033023  

Thanks for merging this! :)  
I will need `BOOST_WORKAROUND` for a patch in Boost.DateTime where `BOOST_DATE_TIME_NO_LOCALE` is defined. (Well, "need" is a relative term - the conditions there use `BOOST_WORKAROUND` and I thought it'd be nicer if I added my condition with `BOOST_WORKAROUND` as well -- it is a workaround after all and I like "uniformity".)

---
