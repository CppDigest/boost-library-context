# #11 MSVC == 1900 scope_exit in lambda fix [Closed]

> Username: tobias-loew  
> Created at: 2018-01-12 12:52:45 UTC  
> Updated at: 2018-01-14 09:22:52 UTC  
> Closed at: 2018-01-13 18:49:31 UTC  
> Url: https://github.com/boostorg/typeof/pull/11  

fixed internal compiler error when using scope-exit inside a lambda with MSVC 1900 (VS 2015)  
  
cf. http://boost.2283326.n4.nabble.com/scope-exit-Boost-gt-1-65-VS-2015-internal-compiler-error-in-lambda-td4700981.html

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2018-01-12 13:00:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/typeof/pull/11#pullrequestreview-88469637  

📁 include/boost/typeof/typeof.hpp

```diff
  17 | 
  18 |- #if !defined(BOOST_NO_CXX11_DECLTYPE) && !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES) && !defined(BOOST_TYPEOF_EMULATION)
  18 |+ #if (!defined(_MSC_VER) || (_MSC_VER > 1900)) && !defined(BOOST_NO_CXX11_DECLTYPE) && !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES) && !defined(BOOST_TYPEOF_EMULATION)
```

> Username: Lastique  
> Created_at: 2018-01-12 13:00:41 UTC  
> Updated_at: 2018-01-13 16:12:35 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#discussion_r161215713  

Since this fix is specific to MSVC, it should test for `BOOST_MSVC`.

> Username: tobias-loew  
> Created_at: 2018-01-12 13:37:34 UTC  
> Updated_at: 2018-01-13 16:12:35 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#discussion_r161222883  

Done


---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2018-01-12 14:28:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/typeof/pull/11#pullrequestreview-88492319  

@pdimov wrote on the mailing list:  
```  
It will be very helpful if you couple this fix with a test  
that demonstrates the breakage (that is, a test that fails  
before the fix and passes after the fix.)  
```

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2018-01-12 14:29:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/typeof/pull/11#pullrequestreview-88492446  

📁 include/boost/typeof/typeof.hpp

```diff
  17 | 
  18 |- #if !defined(BOOST_NO_CXX11_DECLTYPE) && !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES) && !defined(BOOST_TYPEOF_EMULATION)
  18 |+ #if (!defined(BOOST_MSVC) || (BOOST_MSVC > 1900)) && !defined(BOOST_NO_CXX11_DECLTYPE) && !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES) && !defined(BOOST_TYPEOF_EMULATION)
```

> Username: pdimov  
> Created_at: 2018-01-12 14:29:11 UTC  
> Updated_at: 2018-01-13 16:12:35 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#discussion_r161234923  

Should probably use `&& !BOOST_WORKAROUND(BOOST_MSVC, <= 1900)` here, as that's what it is.


---

## Comment 4

> Username: tobias-loew  
> Created_at: 2018-01-12 16:17:48 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357282992  

I've made a pull-request for a test-case in boost/scope_exit since there the error happens. It is connected to boost::type_of::remove_cv_ref_t since the old implementation with boost::type_of::msvc_typeid_wrapper works.   
The following code when inside a lambda results in VS 2015 in an internal compiler error:  
  
typedef void(*boost_se_tag_0_0)(int test);  
  
         
  
        typedef boost::type_of::remove_cv_ref_t<  
  
            decltype(::boost::scope_exit::detail::wrap(::boost::scope_exit::detail::deref(test, static_cast<boost_se_tag_0_0>(0))))  
  
        > boost_se_wrapped_t_0_0;  
  
         
  
        typedef  boost_se_wrapped_t_0_0::type boost_se_capture_t_0_0;

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-01-12 16:58:36 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357294563  

The idea is to put the test case here in order to ensure that it won't be broken again; pull requests to `typeof` do not run the `scope_exit` tests.

---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2018-01-12 17:00:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/typeof/pull/11#pullrequestreview-88540985  

📁 include/boost/typeof/typeof.hpp

```diff
  15 | 
  16 | #include <boost/config.hpp>
```

> Username: pdimov  
> Created_at: 2018-01-12 17:00:57 UTC  
> Updated_at: 2018-01-13 16:12:35 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#discussion_r161274419  

Need to include `<bost/config/workaround.hpp>` here, I suppose.


---

## Comment 7

> Username: tobias-loew  
> Created_at: 2018-01-13 16:11:11 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357446491  

I've added a test case. It doesn't have any test assertions since the test is: either compiles or triggers internal compiler error.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-01-13 16:48:08 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357448903  

Thanks!  
  
Unfortunately, this test fails to compile with `BOOST_TYPEOF_EMULATION` (the Jamfile executes each test with both `BOOST_TYPEOF_NATIVE` and `BOOST_TYPEOF_EMULATION`), and I've no idea why.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-01-13 16:53:21 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357449270  

I figured it out, `BOOST_TYPEOF_REGISTER_TEMPLATE(::detail::wrapper, 1)` was missing.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-01-13 18:49:31 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357457368  

Applied in https://github.com/boostorg/typeof/commit/080f05ea46271c745f7a748815a64210deb0a880.

---

## Comment 11

> Username: tobias-loew  
> Created_at: 2018-01-14 09:22:52 UTC  
> Url: https://github.com/boostorg/typeof/pull/11#issuecomment-357498667  

Great! Thanks!

---
