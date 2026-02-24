# #26 Commented out deprecated include [Closed]

> Username: evelynmitchell  
> Created at: 2016-06-04 04:06:22 UTC  
> Updated at: 2016-06-04 16:04:04 UTC  
> Closed at: 2016-06-04 16:04:04 UTC  
> Url: https://github.com/boostorg/type_traits/pull/26  

https://svn.boost.org/trac/boost/ticket/11886

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-06-04 15:32:30 UTC  
> Url: https://github.com/boostorg/type_traits/pull/26#issuecomment-223761823  

This header file is deprecated, and really should not be included anymore.   
  
You can't remove the header file in your PR because BOOST_TT_AUX_TEMPLATE_ARITY_SPEC, although it expands to nothing, is used with some macros in this header file.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-06-04 16:04:04 UTC  
> Url: https://github.com/boostorg/type_traits/pull/26#issuecomment-223763456  

Right - it's a deprecated header including another deprecated header, IMO it needs to stay as it is.

---
