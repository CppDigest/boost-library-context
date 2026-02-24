# #34 Define BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS for Intel [Closed]

> Username: apolukhin  
> Created at: 2014-08-18 17:09:52 UTC  
> Updated at: 2014-08-22 15:16:53 UTC  
> Closed at: 2014-08-22 15:16:53 UTC  
> Url: https://github.com/boostorg/config/pull/34  

Problem can be seen in regression test reports: [variant](http://www.boost.org/development/tests/develop/developer/output/Sandia-intel-13-1-c++0x-boost-bin-v2-libs-variant-test-recursive_variant_test-test-intel-linux-debug.html), [lexical_cast](http://www.boost.org/development/tests/develop/developer/output/Sandia-intel-13-1-c++0x-boost-bin-v2-libs-lexical_cast-test-lexical_cast_containers_test-test-intel-linux-debug-link-static.html), [logic](http://www.boost.org/development/tests/develop/developer/output/Sandia-intel-13-1-c++0x-boost-bin-v2-libs-logic-test-tribool_io_test-test-intel-linux-debug.html), [xpressive](http://www.boost.org/development/tests/develop/developer/output/Sandia-intel-13-1-c++0x-boost-bin-v2-libs-xpressive-test-regress_u-test-intel-linux-debug-debug-symbols-off-link-static.html) and others...

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-18 17:46:07 UTC  
> Url: https://github.com/boostorg/config/pull/34#issuecomment-52528116  

Hold on a minute, where did this undocumented/untested macro appear from?  
  
BTW this looks to a good candidate for BOOST_WORKAROUND in noncopiable.hpp

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-08-19 07:54:09 UTC  
> Url: https://github.com/boostorg/config/pull/34#issuecomment-52601148  

Well, macro `BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS` already used in gcc.hpp. In this patch I've just been following the existing practice.  
  
noncopyable.hpp [already uses BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS](https://github.com/boostorg/core/blob/master/include/boost/core/noncopyable.hpp)  
  
IMO:  
If feature is broken - it must be marked with BOOST_NO_CXX11_*. So more preferable for me would be totally remove the `BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS` macro and just define `BOOST_NO_CXX11_DEFAULTED_FUNCTIONS` for old GCC and Intel.  
  
Almost the same problems exist with `BOOST_NO_CXX11_VARIADIC_TEMPLATES`. GCC 4.6 has incomplete implementation of variadic templates. MSVC2013 has variadic templates, but they have big issues. I'd better mark all the GCC prior 4.7 and _`MSC_VER <= 1800` with `BOOST_NO_CXX11_VARIADIC_TEMPLATES`.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-19 17:53:48 UTC  
> Url: https://github.com/boostorg/config/pull/34#issuecomment-52672571  

> Well, macro BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS already   
> used in >gcc.hpp. In this patch I've just been following the existing practice.  
> noncopyable.hpp already uses BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS  
  
Sigh: it shouldn't be, the macro simply shouldn't have just been added with no docs or tests or indication of what it's for.  I've just commented on the commit that added it: https://github.com/boostorg/config/commit/411399ec60c0f56e77aa56da830945100a4b263e  
  
> IMO:  
> If feature is broken - it must be marked with BOOST_NO_CXX11_*. So   
> more preferable for me would be totally remove the   
> BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS macro and   
> just define BOOST_NO_CXX11_DEFAULTED_FUNCTIONS for old GCC and Intel.  
  
This is a tricky one: historically we haven't disabled features if they're "mostly working", and then used BOOST_WORKAROUNDS in source when specific bugs crop up.  Of course it's often hard to decide what "mostly working" actually means...

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-08-21 14:02:11 UTC  
> Url: https://github.com/boostorg/config/pull/34#issuecomment-52923643  

See https://github.com/boostorg/config/pull/35. Note that the pull request makes BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS defined when defaulted functions are not supported at all. This pull request can be simplified to reflect that (i.e. remove the check for BOOST_NO_CXX11_DEFAULTED_FUNCTIONS).

---

## Comment 5

> Username: apolukhin  
> Created_at: 2014-08-22 15:16:53 UTC  
> Url: https://github.com/boostorg/config/pull/34#issuecomment-53073357  

Closing this pull request (merged this one with with the #35).

---
