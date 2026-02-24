# #113 - Usage of boost::void_t [Closed]

> Username: ax3l  
> Created at: 2019-02-23 16:49:48 UTC  
> Updated at: 2019-02-27 18:05:12 UTC  
> Closed at: 2019-02-27 18:05:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113  

Boost 1.67.0+ uses `boost::void_t<>` in many traits, although it is only defined if `BOOST_NO_CXX11_TEMPLATE_ALIASES` is not defined.  
  
This causes compile issues on platforms using those includes where template aliases are not available (or `BOOST_NO_CXX11_TEMPLATE_ALIASES` is set).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-23 18:07:10 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-466674458  

>This causes compile issues on platforms using those includes where template aliases are not available (or `BOOST_NO_CXX11_TEMPLATE_ALIASES` is set).  
  
Is there a specific compiler configuration that's causing you problems?  Note that all the traits that use `void_t` do have workarounds in place for older compilers.

---

## Comment 2

> Username: ax3l  
> Created at: 2019-02-23 18:20:19 UTC  
> Updated at: 2019-02-23 18:21:37 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-466675449  

I just saw it with an old work-around with nvcc. it's not like nvcc does not support template aliases, but we used the work-around to deactivate another issue for some time.  
  
[`void_t`](https://github.com/boostorg/type_traits/blob/boost-1.67.0/include/boost/type_traits/make_void.hpp#L39-L46) has no fallback, but is used [in regular traits](https://github.com/boostorg/type_traits/blob/boost-1.67.0/include/boost/type_traits/has_minus.hpp#L55) since 1.67.0  
  
Don't need it therefore anymore, just wanted to make sure you aware that the logic of guarding `void_t` creation does not work with the rest anymore.

---

## Comment 3

> Username: glenfe  
> Created at: 2019-02-23 21:00:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-466694310  

Any `void_t<X>` can be `typename make_void<X>::type` to support implementations that do not support C++11 template aliases.

---

## Comment 4

> Username: ax3l  
> Created at: 2019-02-23 21:04:38 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-466694922  

Yes, that's what I am suggesting: use `boost::make_void<>::type` in those places?

---

## Comment 5

> Username: glenfe  
> Created at: 2019-02-27 13:47:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-467867570  

Pull request #115

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-02-27 18:05:12 UTC  
> Url: https://github.com/boostorg/type_traits/issues/113#issuecomment-467967844  

Merged to develop.
