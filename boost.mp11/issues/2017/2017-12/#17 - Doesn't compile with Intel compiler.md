# #17 - Doesn't compile with Intel compiler [Closed]

> Username: rolandschulz  
> Created at: 2017-12-20 05:38:38 UTC  
> Updated at: 2018-01-23 16:49:14 UTC  
> Closed at: 2018-01-23 16:49:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/17  

Intel compiler isn't listed as supported thus this is a feature request not a bug report.  
  
Using the below alternative implementation (slight modification of the cppreference implementation of is_detected) for mp_valid reduces the failed tests from 9 to 2. Would it make sense to upload this with BOOST_WORKAROUND(BOOST_INTEL) as pull request? I currently have no idea how to work around the remaining issue(s). Any suggestion/help would be welcome.  
  
```  
namespace detail  
{  
template<typename...> using void_t = void;  
  
template <class, template<class...> class F, class... T>  
struct mp_valid_impl : mp_false {};  
  
template <template<class...> class F, class... T>  
struct mp_valid_impl<void_t<F<T...>>, F, T...> : mp_true {};  
} // namespace detail                                                                                                                                                                                                                           
  
template<template<class...> class F, class... T> using mp_valid = typename detail::mp_valid_impl<void, F, T...>;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-20 13:30:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353063448  

> Would it make sense to upload this with BOOST_WORKAROUND(BOOST_INTEL) as pull request?  
  
Yes of course. Would be better to use 'mp_void', although it's currently in `function.hpp` and `utility.hpp` is lower level, so I'll have to rearrange things a bit first.  
  
Which tests remain?

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-20 14:00:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353070266  

> so I'll have to rearrange things a bit first.  
  
Done in https://github.com/boostorg/mp11/commit/ea073efb43646cd7c7a4ff268a45fe3538a1141f.

---

## Comment 3

> Username: rolandschulz  
> Created at: 2017-12-20 18:00:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353137352  

Using mp_void makes the work-around not work (mp_void is fine for mp_and.cpp). With that still 9 tests fail. With the void_t the two failing tests are mp_eval_if_sf.cpp and mp_invoke_sf.cpp. mp_eval_if_sf.cpp is fine without the work-around or with the work-around using mp_void. mp_invoke_sf.cpp fails with or without the work-around.  
  
first failure in mp_eval_if_sf:  
```  
./boost/mp11/utility.hpp(163): error: class "boost::mp11::detail::mp_no_type" has no member "type"  
      template<class... T> using fn = typename mp_defer<F, T...>::type;  
                                                                  ^  
          detected during:  
            instantiation of type "boost::mp11::mp_quote<F>::fn<> [with F=boost::mp11::mp_identity_t]" at line 100  
            instantiation of class "boost::mp11::detail::mp_defer_impl<F, T...> [with F=boost::mp11::mp_quote<boost::mp11::mp_identity_t>::fn, T=<>]" at line 122  
            instantiation of class "boost::mp11::detail::mp_eval_if_c_impl<false, T, F, U...> [with T=void, F=boost::mp11::mp_quote<boost::mp11::mp_identity_t>::fn, U=<>]" at line 32 of "./boost/core/lightweight_test_trait.hpp"  
            instantiation of "void boost::detail::test_trait_impl(const char *, void (*)(T), bool, const char *, int, const char *) [with T=boost::mp11::mp_valid<boost::mp11::mp_eval_if_q, std::false_type, void, boost::mp11::mp_quote<boost::mp11::mp_identity_t>>]" at line 41 of "libs/mp11/test/mp_eval_if_sf.cpp"  
```  
  
first in mp_invoke_sf:  
```  
./boost/mp11/utility.hpp(163): error: class "boost::mp11::detail::mp_no_type" has no member "type"  
      template<class... T> using fn = typename mp_defer<F, T...>::type;  
                                                                  ^  
          detected during:  
            instantiation of type "boost::mp11::mp_quote<F>::fn<> [with F=boost::mp11::mp_identity_t]" at line 32 of "./boost/core/lightweight_test_trait.hpp"  
            instantiation of "void boost::detail::test_trait_impl(const char *, void (*)(T), bool, const char *, int, const char *) [with T=boost::mp11::mp_valid<boost::mp11::mp_invoke, boost::mp11::mp_quote<boost::mp11::mp_identity_t>>]" at line 30 of "libs/mp11/test/mp_invoke_sf.cpp"  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2017-12-20 18:06:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353139204  

> Using mp_void makes the work-around not work  
  
Interesting. Now that you mention it, this does make sense. But the implication is that `mp_void` is broken, as it's supposed to be the same as `void_t`. I remember that the obvious definition used to not work on some old compilers, but don't recall which at the moment.  
  
I guess I'll just change it and see if this breaks anything.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-12-20 18:27:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353144327  

OK done: https://github.com/boostorg/mp11/commit/860f918553a37c86e4f356107c02ec481e49348f.  
  
> instantiation of type "boost::mp11::mp_quote<F>::fn<> [with F=boost::mp11::mp_identity_t]"  
  
This implies that `mp_valid<mp_identity_t>` is false, although I think I had a test for that?

---

## Comment 6

> Username: rolandschulz  
> Created at: 2017-12-20 19:18:21 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353157205  

The old mp_void worked for mp_and and it also worked for mp_valid for the tests in mp_eval_if_sf. But the old mp_valid or the work-around mp_valid with the old mp_void give in mp_eval_if.cpp:  
```  
./boost/mp11/utility.hpp(130): error: class "boost::mp11::detail::mp_eval_if_c_impl<false, char [], boost::mp11::mp_quote<boost::mp11::mp_identity>::fn, void ()>" has no member "type"  
  template<class C, class T, class Q, class... U> using mp_eval_if_q = typename detail::mp_eval_if_c_impl<static_cast<bool>(C::value), T, Q::template fn, U...>::type;  
                                                                                                                                                                 ^  
          detected during instantiation of type "boost::mp11::mp_eval_if_q<std::false_type, char [], boost::mp11::mp_quote<boost::mp11::mp_identity>, void ()>" at line 33 of "libs/mp11/test/mp_eval_if.cpp"  
```  
  
There is indeed a test for `mp_valid<mp_identity_t>` in mp_valid.cpp. And it is fine with all 3 version (no workaround, and workaround with both versions of mp_void). The problem is that mp_valid as part of more complicated expressions fails.  
  
BTW: Would you like to test for yourself and don't have a license to do so?

---

## Comment 7

> Username: pdimov  
> Created at: 2017-12-20 19:59:15 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353167215  

This might be best; I'm on Windows. Not sure when I'll find the time to install yet another toolset though.  
  
> There is indeed a test for mp_valid<mp_identity_t> in mp_valid.cpp.  
  
Actually now that I look at it, there isn't, there's one for `mp_identity` and this is not the same thing. :-)

---

## Comment 8

> Username: rolandschulz  
> Created at: 2017-12-20 20:38:50 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353176175  

Indeed. Didn't pay attention to the _t :). But if I add one for mp_identity_t that passes.  
  
I'll ask for the license.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-12-20 20:43:12 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353177214  

I requested an open source license from the Intel site; that's Linux-only but it will do. The request will apparently be processed within two business days. :-)

---

## Comment 10

> Username: pdimov  
> Created at: 2017-12-21 20:22:22 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353448850  

Unfortunately, it doesn't appear that `mp_valid` can be made to work with `mp_quote<F>::fn` on this compiler, at least with this workaround and a few others that came to mind. While using `mp_void` makes `mp_valid` report false when it should be true, using `void_t` just makes it report true when it should be false.  
  
Making SFINAE tricks work reliably across compilers is such a pain.

---

## Comment 11

> Username: pdimov  
> Created at: 2017-12-21 21:49:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-353466158  

OK, it seems that this is the best we can do here. Roland, would you please prepare the pull request with the workaround?

---

## Comment 12

> Username: pdimov  
> Created at: 2018-01-23 16:49:14 UTC  
> Url: https://github.com/boostorg/mp11/issues/17#issuecomment-359854448  

In https://github.com/boostorg/mp11/commit/c2f8024305c012deb95e4787f64ccc006b8a71e0.
