# #25 Fixes `"noreturn" does return` warning in release mode. #24 follow-up [Merged]

> Username: Kojoley  
> Created at: 2016-09-02 19:19:14 UTC  
> Updated at: 2016-09-04 10:01:43 UTC  
> Merged at: 2016-09-03 08:13:38 UTC  
> Closed at: 2016-09-03 08:13:38 UTC  
> Url: https://github.com/boostorg/variant/pull/25  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-09-03 09:03:42 UTC  
> Url: https://github.com/boostorg/variant/pull/25#issuecomment-244535787  

I've changed a few things in https://github.com/boostorg/variant/commit/9342505270e6e0b9396937466a6fb803186827fd.  
  
`std::terminate` is declared in header `<exception>`, so that header must be included, but in cases when exceptions are disabled this may lead to problems. Changed `std::terminate` to `abort`, left only a single `forced_noreturn()` function, tried to leverage `[[noreturn]]` attributes whenewer it is possible.  
  
I have no warnings on gcc-4.8 and clang-3.7 with flags "-Wall -Wextra -Wmissing-noreturn" in debug and release modes.  
  
Could you please try the new version and make sure that there's no warnings on your compiler?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-09-03 16:14:58 UTC  
> Url: https://github.com/boostorg/variant/pull/25#issuecomment-244555296  

Checked on msvc-14, gcc-4.9.2, clang-3.5.0, clang-3.8.1 with and w/o`BOOST_EXCEPTION_DISABLE` (`BOOST_NO_EXCEPTIONS` produces linkage errors, but not compile warnings) - previous warning has not show up, but I faced others.  
Warnings from gcc:  
  
```  
In file included from ../../../boost/variant/variant.hpp:27:0,  
                 from variant_get_test.cpp:13:  
../../../boost/variant/detail/backup_holder.hpp: In copy constructor ‘boost::detail::variant::backup_holder<T>::backup_holder(const boost::detail::variant::backup_holder<T>&) [with T = base]’:  
../../../boost/variant/detail/backup_holder.hpp:79:1: warning: function might be candidate for attribute ‘noreturn’ [-Wsuggest-attribute=noreturn]  
 backup_holder<T>::backup_holder(const backup_holder&)  
 ^  
```  
  
(can be fixed like this https://github.com/Kojoley/variant/commit/aa5ac0032f1b7ec4cec5ce40a039fa926616ee9a)  
  
and:  
  
```  
In file included from ../../../boost/variant.hpp:17:0,  
                 from rvalue_test.cpp:15:  
../../../boost/variant/variant.hpp: In function ‘void run()’:  
../../../boost/variant/variant.hpp:2237:21: warning: ‘*((void*)& tmp +4)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             variant tmp( detail::variant::move(rhs) );  
                     ^  
```  
  
Also got one from msvc that related to the test code:  
  
```  
variant_get_test.cpp  
variant_get_test.cpp(61): warning C4127: conditional expression is constant  
variant_get_test.cpp(193): note: see reference to function template instantiation 'void check_polymorphic_get_on_types_impl_single_type<derived2,var_t_shortened,int>(V *)' being compiled  
        with  
        [  
            V=var_t_shortened  
        ]  
variant_get_test.cpp(95): warning C4127: conditional expression is constant  
variant_get_test.cpp(213): note: see reference to function template instantiation 'void check_get_on_types_impl_single_type<base,var_t_no_fallback,base>(V *)' being compiled  
        with  
        [  
            V=var_t_no_fallback  
        ]  
```  
  
#   
  
All tests were done with `b2 -j2 toolset=... variant=debug,release warnings=extra noreturn-warning=on [define=BOOST_NO_EXCEPTIONS|define=BOOST_EXCEPTION_DISABLE]`  
  
`user-config.jam` content is:  
  
```  
import feature ;  
import toolset ;  
  
feature.extend warnings : extra ;  
toolset.flags msvc.compile OPTIONS <warnings>extra : /W4 : unchecked ;  
toolset.flags gcc.compile OPTIONS <warnings>extra : -Wall -Wextra : unchecked ;  
toolset.flags clang-linux.compile OPTIONS <warnings>extra : -Wall -Wextra : unchecked ;   
  
feature.feature noreturn-warning : off on : optional incidental propagated ;  
toolset.flags msvc.compile OPTIONS <noreturn-warning>on : /w44702 /w44715 : unchecked ;  
toolset.flags gcc.compile OPTIONS <noreturn-warning>on : -Wunreachable-code -Wmissing-noreturn : unchecked ;  
toolset.flags clang-linux.compile OPTIONS <noreturn-warning>on : -Wunreachable-code -Wmissing-noreturn : unchecked ;   
```

---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-09-04 10:01:43 UTC  
> Url: https://github.com/boostorg/variant/pull/25#issuecomment-244593861  

Fixing `backup_holder` during beta seems to be a bad idea. I'm not sure that all the compilers allow marking constructors with noreturn. And anyway, that function must not be called, so it's better to remove it, rewrite some code and make sure that this function is not called at compile time. I'll try to fix that some day.

---
