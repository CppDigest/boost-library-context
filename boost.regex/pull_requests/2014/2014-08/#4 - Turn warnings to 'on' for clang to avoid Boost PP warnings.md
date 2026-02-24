# #4 Turn warnings to 'on' for clang to avoid Boost PP warnings. [Closed]

> Username: eldiener  
> Created at: 2014-08-04 23:25:09 UTC  
> Updated at: 2014-08-17 18:38:37 UTC  
> Closed at: 2014-08-16 18:14:50 UTC  
> Url: https://github.com/boostorg/regex/pull/4  

If warnings are 'all' many clang warnings in Boost PP occur.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-15 17:41:50 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52335804  

I appreciate why this patch has been submitted.  However, my experience has been that many regex users really do want to be able to compile with -pedantic and if they can't they complain :-(  
  
For that reason I would prefer to continue to build and test with <warnings>all to expose any problems.  Is this really unfixable in the preprocessor lib?

---

## Comment 2

> Username: eldiener  
> Created_at: 2014-08-16 00:03:13 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52374950  

Would a pull request with clang having the options '-Wno-c99-extensions' and '-Wno-variadic-macros' turned on, without removing '-pedantic' by keeping the warnings as 'all', in the regex build jamfile be acceptable ?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-16 08:05:20 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52387018  

Good question - my gut feeling is that the status quo represents a common real-world use case that folks expect to see warning free.  However, I'll try and fire up Linux and clang later and see how bad things are for myself ;-)

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2014-08-16 18:14:50 UTC  
> Updated_at: 2014-08-16 18:15:59 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52401565  

I believe this is best fixed in the preprocessor library, some experimenting suggests that adding:  
  
#if defined(**clang**) && defined(**GNUC**)  
#pragma GCC system_header  
#endif  
  
To the files emitting the warnings fixes this rather nicely.  
  
Alternatively:  
  
# ifdef **clang**  
#   pragma clang diagnostic push  
#   pragma clang diagnostic ignored "-Wvariadic-macros"  
# endif  
  
// variadic macros defined  
  
# ifdef **clang**  
#   pragma clang diagnostic pop  
# endif  
  
Also works and is rather more finely grained in it's control.

---

## Comment 5

> Username: eldiener  
> Created_at: 2014-08-17 14:10:01 UTC  
> Updated_at: 2014-08-17 14:14:15 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52422872  

Please do not close this until I can respond.  
  
I do not believe that declaring all the affected preprocessor headers as system headers is the right   solution, even if it might work in practice. Turning off all warnings in those headers for gcc and clang cannot be correct IMO, and who knows what else it means in GCC/clang to pretend that one's header file is a system header file. I really do not want to take that chance.  
  
Your secondary solution works with the "-Wvariadic-macros" diagnostic works just fine. But it does not work on clang, although it should, with the "-Wc99-extensions" diagnostic. There is plenty of such verbose output stemming from a single line in variadic/elem.hpp. Yet my attempt to surround that line with:  
  
 # ifdef **clang**  
 # pragma clang diagnostic push  
 # pragma clang diagnostic ignored "-Wc99-extensions"  
 # endif  
  
 // The line etc.  
  
 # ifdef **clang**  
 # pragma clang diagnostic pop  
 # endif  
  
fails to eliminate he warning. This seems like a clang bug.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2014-08-17 17:51:42 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52429295  

Edward, there's no good solution here - changing the command line certainly works, but then users will still see the warnings in their own code (including from header only libraries) and no doubt complain.  I just don't see this as the right solution.  
  
Here's the fix I've just tested with current develop and current clang svn:  
  
diff --git a/include/boost/preprocessor/facilities/overload.hpp b/include/boost/preprocessor/facilities/overload.hpp  
index 1576316..bdffb6b 100644  
--- a/include/boost/preprocessor/facilities/overload.hpp  
+++ b/include/boost/preprocessor/facilities/overload.hpp  
@@ -19,7 +19,18 @@  
 # /\* BOOST_PP_OVERLOAD _/  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    define BOOST_PP_OVERLOAD(prefix, ...) BOOST_PP_CAT(prefix, BOOST_PP_VARIADIC_SIZE(**VA_ARGS**))  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # endif  
 #  
 # endif  
diff --git a/include/boost/preprocessor/tuple/eat.hpp b/include/boost/preprocessor/tuple/eat.hpp  
index 708015e..a6371e9 100644  
--- a/include/boost/preprocessor/tuple/eat.hpp  
+++ b/include/boost/preprocessor/tuple/eat.hpp  
@@ -20,7 +20,18 @@  
 # /_ BOOST_PP_EAT _/  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    define BOOST_PP_EAT(...)  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # else  
 #    define BOOST_PP_EAT(x)  
 # endif  
diff --git a/include/boost/preprocessor/tuple/elem.hpp b/include/boost/preprocessor/tuple/elem.hpp  
index 0ee8a3f..4969055 100644  
--- a/include/boost/preprocessor/tuple/elem.hpp  
+++ b/include/boost/preprocessor/tuple/elem.hpp  
@@ -24,6 +24,12 @@  
 # include <boost/preprocessor/tuple/detail/is_single_return.hpp>  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    if BOOST_PP_VARIADICS_MSVC  
 #        define BOOST_PP_TUPLE_ELEM(...) BOOST_PP_TUPLE_ELEM_I(BOOST_PP_OVERLOAD(BOOST_PP_TUPLE_ELEM_O_, **VA_ARGS**), (**VA_ARGS**))  
 #        define BOOST_PP_TUPLE_ELEM_I(m, args) BOOST_PP_TUPLE_ELEM_II(m, args)  
@@ -41,6 +47,11 @@  
 #       define BOOST_PP_TUPLE_ELEM_O_2(n, tuple) BOOST_PP_VARIADIC_ELEM(n, BOOST_PP_REM tuple)  
 #    endif  
 #    define BOOST_PP_TUPLE_ELEM_O_3(size, n, tuple) BOOST_PP_TUPLE_ELEM_O_2(n, tuple)  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # else  
 #    if BOOST_PP_CONFIG_FLAGS() & BOOST_PP_CONFIG_MSVC()  
 #        define BOOST_PP_TUPLE_ELEM(size, n, tuple) BOOST_PP_TUPLE_ELEM_I(BOOST_PP_CAT(BOOST_PP_TUPLE_ELEM_, n), BOOST_PP_CAT(BOOST_PP_CAT(BOOST_PP_TUPLE_ELEM_E_, size), tuple))  
diff --git a/include/boost/preprocessor/tuple/rem.hpp b/include/boost/preprocessor/tuple/rem.hpp  
index fe8a7d2..0243a93 100644  
--- a/include/boost/preprocessor/tuple/rem.hpp  
+++ b/include/boost/preprocessor/tuple/rem.hpp  
@@ -22,11 +22,22 @@  
 # /_ BOOST_PP_REM _/  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #   if BOOST_PP_VARIADICS_MSVC  
        /_ To be used internally when **VA_ARGS** could be empty ( or is a single element ) _/  
 #      define BOOST_PP_REM_CAT(...) BOOST_PP_CAT(**VA_ARGS**,)  
 #   endif  
 #    define BOOST_PP_REM(...) **VA_ARGS**  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # else  
 #    define BOOST_PP_REM(x) x  
 # endif  
@@ -120,6 +131,12 @@  
 # /_ BOOST_PP_TUPLE_REM_CTOR _/  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    if BOOST_PP_VARIADICS_MSVC  
 #        define BOOST_PP_TUPLE_REM_CTOR(...) BOOST_PP_TUPLE_REM_CTOR_I(BOOST_PP_OVERLOAD(BOOST_PP_TUPLE_REM_CTOR_O_, **VA_ARGS**), (**VA_ARGS**))  
 #        define BOOST_PP_TUPLE_REM_CTOR_I(m, args) BOOST_PP_TUPLE_REM_CTOR_II(m, args)  
@@ -130,6 +147,11 @@  
 #       define BOOST_PP_TUPLE_REM_CTOR_O_1(tuple) BOOST_PP_REM tuple  
 #    endif  
 #    define BOOST_PP_TUPLE_REM_CTOR_O_2(size, tuple) BOOST_PP_TUPLE_REM_CTOR_O_1(tuple)  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # else  
 #    if ~BOOST_PP_CONFIG_FLAGS() & BOOST_PP_CONFIG_EDG()  
 #        define BOOST_PP_TUPLE_REM_CTOR(size, tuple) BOOST_PP_TUPLE_REM_CTOR_I(BOOST_PP_TUPLE_REM(size), tuple)  
diff --git a/include/boost/preprocessor/variadic/elem.hpp b/include/boost/preprocessor/variadic/elem.hpp  
index be38a94..396b8d1 100644  
--- a/include/boost/preprocessor/variadic/elem.hpp  
+++ b/include/boost/preprocessor/variadic/elem.hpp  
@@ -19,6 +19,12 @@  
 # /_ BOOST_PP_VARIADIC_ELEM _/  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    if BOOST_PP_VARIADICS_MSVC  
 #        define BOOST_PP_VARIADIC_ELEM(n, ...) BOOST_PP_VARIADIC_ELEM_I(n,**VA_ARGS**)  
 #        define BOOST_PP_VARIADIC_ELEM_I(n, ...) BOOST_PP_CAT(BOOST_PP_CAT(BOOST_PP_VARIADIC_ELEM_, n)(**VA_ARGS**,),)  
@@ -91,4 +97,8 @@  
 #    define BOOST_PP_VARIADIC_ELEM_63(e0, e1, e2, e3, e4, e5, e6, e7, e8, e9, e10, e11, e12, e13, e14, e15, e16, e17, e18, e19, e20, e21, e22, e23, e24, e25, e26, e27, e28, e29, e30, e31, e32, e33, e34, e35, e36, e37, e38, e39, e40, e41, e42, e43, e44, e45, e46, e47, e48, e49, e50, e51, e52, e53, e54, e55, e56, e57, e58, e59, e60, e61, e62, e63, ...) e63  
 # endif  
 #  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # endif  
diff --git a/include/boost/preprocessor/variadic/size.hpp b/include/boost/preprocessor/variadic/size.hpp  
index b92a5ff..40df2f8 100644  
--- a/include/boost/preprocessor/variadic/size.hpp  
+++ b/include/boost/preprocessor/variadic/size.hpp  
@@ -19,6 +19,12 @@  
 # /_ BOOST_PP_VARIADIC_SIZE */  
 #  
 # if BOOST_PP_VARIADICS  
+#  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wvariadic-macros"  
+#    endif  
+#  
 #    if BOOST_PP_VARIADICS_MSVC  
 #        define BOOST_PP_VARIADIC_SIZE(...) BOOST_PP_CAT(BOOST_PP_VARIADIC_SIZE_I(**VA_ARGS**, 64, 63, 62, 61, 60, 59, 58, 57, 56, 55, 54, 53, 52, 51, 50, 49, 48, 47, 46, 45, 44, 43, 42, 41, 40, 39, 38, 37, 36, 35, 34, 33, 32, 31, 30, 29, 28, 27, 26, 25, 24, 23, 22, 21, 20, 19, 18, 17, 16, 15, 14, 13, 12, 11, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1,),)  
 #    else  
@@ -27,4 +33,8 @@  
 #    define BOOST_PP_VARIADIC_SIZE_I(e0, e1, e2, e3, e4, e5, e6, e7, e8, e9, e10, e11, e12, e13, e14, e15, e16, e17, e18, e19, e20, e21, e22, e23, e24, e25, e26, e27, e28, e29, e30, e31, e32, e33, e34, e35, e36, e37, e38, e39, e40, e41, e42, e43, e44, e45, e46, e47, e48, e49, e50, e51, e52, e53, e54, e55, e56, e57, e58, e59, e60, e61, e62, e63, size, ...) size  
 # endif  
 #  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+#  
 # endif  
  
In addition a patch to mpl is required:  
  
diff --git a/include/boost/mpl/aux_/include_preprocessed.hpp b/include/boost/mpl/aux_/include_preprocessed.hpp  
index c13434c..cb90a44 100644  
--- a/include/boost/mpl/aux_/include_preprocessed.hpp  
+++ b/include/boost/mpl/aux_/include_preprocessed.hpp  
@@ -34,7 +34,18 @@  
 #   include AUX778076_INCLUDE_STRING  
 #   undef AUX778076_INCLUDE_STRING  
 #else  
+  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wc99-extensions"  
+#    endif  
+  
 #   include BOOST_PP_STRINGIZE(boost/mpl/aux_/preprocessed/AUX778076_PREPROCESSED_HEADER)  
+  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+  
 #endif  
  
 #   undef AUX778076_PREPROCESSED_HEADER  
diff --git a/include/boost/mpl/if.hpp b/include/boost/mpl/if.hpp  
index b6bdf6c..f2b814a 100644  
--- a/include/boost/mpl/if.hpp  
+++ b/include/boost/mpl/if.hpp  
@@ -128,8 +128,17 @@ struct if_  
  
 #endif // BOOST_NO_TEMPLATE_PARTIAL_SPECIALIZATION  
  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wc99-extensions"  
+#    endif  
+  
 BOOST_MPL_AUX_NA_SPEC(3, if_)  
  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+  
 }}  
  
 #endif // BOOST_MPL_IF_HPP_INCLUDED  
diff --git a/include/boost/mpl/not.hpp b/include/boost/mpl/not.hpp  
index d5f6025..2e88850 100644  
--- a/include/boost/mpl/not.hpp  
+++ b/include/boost/mpl/not.hpp  
@@ -44,8 +44,17 @@ struct not_  
     BOOST_MPL_AUX_LAMBDA_SUPPORT(1,not_,(T))  
 };  
  
+#    ifdef **clang**  
+#       pragma clang diagnostic push  
+#       pragma clang diagnostic ignored "-Wc99-extensions"  
+#    endif  
+  
 BOOST_MPL_AUX_NA_SPEC(1,not_)  
  
+#    ifdef **clang**  
+#       pragma clang diagnostic pop  
+#    endif  
+  
 }}  
  
 #endif // BOOST_MPL_NOT_HPP_INCLUDED  
  
With that regex compiles cleanly without all the warnings.

---

## Comment 7

> Username: eldiener  
> Created_at: 2014-08-17 18:38:37 UTC  
> Url: https://github.com/boostorg/regex/pull/4#issuecomment-52430621  

I am aware of this type of fix. Please see my latest post on the boost developers mailing list.  
  
The issue is that while the -Wvariadic-macros warnings can be handled at the Boost PP level, which I also have already done locally, the -Wc99-extensions warnings must be all handled at their point of origin, as you have done above. But as soon as another macro ends up calling the Boost PP macro which causes the -Wc99-extensions warning, that has to be handled at the top-level again. And if other macros are added to Boost PP, or any other macro library, which induce the clang -Wc99-extensions warning, it again has to be handled at the top level as you do above. This does not scale very well as a solution as I am sure you will agree. So while my local Boost PP changes ( mimicking yours above ) solves the -Wvariadic-macros warnings problem, the -Wc99-extensions warning problem will always have to be solved again and again at some top-level macro eventually calling a variadic macro which produces the warning in clang.

---
