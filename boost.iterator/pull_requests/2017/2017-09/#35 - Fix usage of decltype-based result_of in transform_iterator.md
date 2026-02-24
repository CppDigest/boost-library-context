# #35 Fix usage of decltype-based result_of in transform_iterator. [Merged]

> Username: morinmorin  
> Created at: 2017-09-16 09:07:44 UTC  
> Updated at: 2017-09-19 17:50:49 UTC  
> Merged at: 2017-09-17 22:23:44 UTC  
> Closed at: 2017-09-17 22:23:44 UTC  
> Url: https://github.com/boostorg/iterator/pull/35  

Regardless of value categories of a `transform_iterator` object, its dereference operator calls `m_f` as an lvalue. Thus, correct usage of `result_of` in C++11 is `result_of<const UnaryFunc&(...)>`.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-19 11:11:09 UTC  
> Url: https://github.com/boostorg/iterator/pull/35#issuecomment-330506845  

I think, this PR broke MSVC-12:  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08-e-win2012R2-64on64-boost-bin-v2-libs-iterator-test-transform_iterator_test-test-msvc-12-0-dbg-adrs-mdl-64-thrd-mlt.html  
  
@morinmorin, could you have a look?

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-09-19 12:18:57 UTC  
> Url: https://github.com/boostorg/iterator/pull/35#issuecomment-330521010  

Sorry for the breakage. I'll take a look today.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2017-09-19 13:48:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/35#issuecomment-330544737  

I don't have MSVC, but I succeeded to reproduce the error on g++ by defining `BOOST_NO_SFINAE_EXPR`. The code in `#ifdef BOOST_NO_SFINAE_EXPR` block in `result_of_iterate.hpp` is for implementing SFINAE-friendly `result_of` on compilers without expression SFINAE. It seems that that code does not support references to function pointers; the following code fails to compile:  
```  
using FP = int(*)(int);  
using Rt = result_of<FP&(int)>::type;  
```  
  
Looking into it further now.

---

## Comment 4

> Username: morinmorin  
> Created_at: 2017-09-19 17:50:49 UTC  
> Url: https://github.com/boostorg/iterator/pull/35#issuecomment-330619021  

OK, I'll submit a PR to Boost.ResultOf tomorrow.  
```diff  
--- a/include/boost/utility/detail/result_of_iterate.hpp  
+++ b/include/boost/utility/detail/result_of_iterate.hpp  
@@ -82,15 +82,12 @@ struct BOOST_PP_CAT(result_of_callable_fun_2_, BOOST_PP_ITERATION())<R(BOOST_PP_  
 };  
   
 template<typename F>  
-struct BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION());  
-  
-template<typename F>  
-struct BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())<F *>  
+struct BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())  
   : BOOST_PP_CAT(result_of_callable_fun_2_, BOOST_PP_ITERATION())<F>  
 {};  
   
 template<typename F>  
-struct BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())<F &>  
+struct BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())<F *>  
   : BOOST_PP_CAT(result_of_callable_fun_2_, BOOST_PP_ITERATION())<F>  
 {};  
   
@@ -99,7 +96,7 @@ struct BOOST_PP_CAT(result_of_select_call_wrapper_type_, BOOST_PP_ITERATION())  
   : mpl::eval_if<  
         is_class<typename remove_reference<F>::type>,  
         result_of_wrap_callable_class<F>,  
-        mpl::identity<BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())<typename remove_cv<F>::type> >  
+        mpl::identity<BOOST_PP_CAT(result_of_callable_fun_, BOOST_PP_ITERATION())<typename remove_cv<typename remove_reference<F>::type>::type> >  
     >  
 {};  
```

---
