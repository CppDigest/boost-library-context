# #606 - Deprecated Boost macros [Closed]

> Username: jszuppe  
> Created at: 2016-05-01 12:14:15 UTC  
> Updated at: 2016-05-04 06:57:42 UTC  
> Closed at: 2016-05-04 06:57:40 UTC  
> Url: https://github.com/boostorg/compute/issues/606  

In [config.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/config.hpp) there are still deprecated Boost macros like `BOOST_NO_RVALUE_REFERENCES` or `BOOST_NO_VARIADIC_TEMPLATES` (most of them were deprecated in 1.50 or 1.51).   
  
I have changes ready if I want to update and remove those deprecated macros, but that would mean no further support for Boost 1.48. Boost 1.51 will have to  become the minimum required Boost version, but I think 1.55 would be the better decision as it seems to be more popular version (it's available in Ubuntu Trusty 14.04 LTS) + there were some new macro changes in 1.53 version (see [deprecated boost macros](http://www.boost.org/doc/libs/1_60_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.boost_deprecated_macros)).   
  
To be honest I've just noticed that with `BOOST_NO_CXX11_NUMERIC_LIMITS` used in [literal.hpp](https://github.com/boostorg/compute/blob/develop/include/boost/compute/detail/literal.hpp) we already have lost support for Boost <1.51 (before 1.51 it was `BOOST_NO_NUMERIC_LIMITS_LOWEST`).

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-05-03 02:37:33 UTC  
> Url: https://github.com/boostorg/compute/issues/606#issuecomment-216422287  

Yeah, I think we should start removing usages of the older versions of the config macros.  
  
Looks like the Ubuntu 14.04 supports Boost 1.54 (which itself was released almost three years ago). I think that would probably be a good new minimum version.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-05-03 08:55:54 UTC  
> Url: https://github.com/boostorg/compute/issues/606#issuecomment-216471863  

OK
