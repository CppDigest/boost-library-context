# #25 Fix for Ticket #11954 [Closed]

> Username: scopeInfinity  
> Created at: 2016-02-06 17:09:24 UTC  
> Updated at: 2016-12-04 00:26:11 UTC  
> Closed at: 2016-12-04 00:26:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25  



---

## Comment 1

> Username: saturnjct  
> Created_at: 2016-03-28 19:39:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-202550291  

Any chance that this change can make it into Boost 1.61.0?

---

## Comment 2

> Username: mattgodbolt  
> Created_at: 2016-04-01 12:41:36 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-204382844  

+1 - the deprecation warnings are rather annoying in our otherwise-warning-free code.

---

## Comment 3

> Username: bc-lee  
> Created_at: 2016-05-17 08:05:02 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-219647499  

Well, It hasn't fixed yet in Boost 1.61 :(.

---

## Comment 4

> Username: nigels-com  
> Created_at: 2016-05-27 08:18:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-222088226  

$ patch -p0 < boost.patch  
  
```  
diff -ru type_traits.original/broken_compiler_spec.hpp type_traits/broken_compiler_spec.hpp  
--- type_traits.original/broken_compiler_spec.hpp   2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/broken_compiler_spec.hpp    2016-05-27 17:48:35.023137408 +1000  
@@ -13,7 +13,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (boost/type_traits/broken_compiler_spec.hpp) is deprecated")  
+//("NOTE: Use of this header (boost/type_traits/broken_compiler_spec.hpp) is deprecated")  
 #endif  
  
 #endif // BOOST_TT_CONFIG_HPP_INCLUDED  
diff -ru type_traits.original/config.hpp type_traits/config.hpp  
--- type_traits.original/config.hpp 2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/config.hpp  2016-05-27 17:48:35.023137408 +1000  
@@ -13,7 +13,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (boost/type_traits/config.hpp) is deprecated")  
+//("NOTE: Use of this header (boost/type_traits/config.hpp) is deprecated")  
 #endif  
  
 #endif // BOOST_TT_CONFIG_HPP_INCLUDED  
diff -ru type_traits.original/detail/bool_trait_def.hpp type_traits/detail/bool_trait_def.hpp  
--- type_traits.original/detail/bool_trait_def.hpp  2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/bool_trait_def.hpp   2016-05-27 17:49:04.646825359 +1000  
@@ -15,7 +15,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (bool_trait_def.hpp) is deprecated")  
+//("NOTE: Use of this header (bool_trait_def.hpp) is deprecated")  
 #endif  
  
 #include <boost/type_traits/detail/template_arity_spec.hpp>  
diff -ru type_traits.original/detail/ice_and.hpp type_traits/detail/ice_and.hpp  
--- type_traits.original/detail/ice_and.hpp 2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/ice_and.hpp  2016-05-27 17:49:04.646825359 +1000  
@@ -15,7 +15,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (ice_and.hpp) is deprecated")  
+//("NOTE: Use of this header (ice_and.hpp) is deprecated")  
 #endif  
  
 namespace boost {  
diff -ru type_traits.original/detail/ice_eq.hpp type_traits/detail/ice_eq.hpp  
--- type_traits.original/detail/ice_eq.hpp  2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/ice_eq.hpp   2016-05-27 17:49:04.646825359 +1000  
@@ -14,7 +14,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (ice_eq.hpp) is deprecated")  
+//("NOTE: Use of this header (ice_eq.hpp) is deprecated")  
 #endif  
  
 namespace boost {  
diff -ru type_traits.original/detail/ice_not.hpp type_traits/detail/ice_not.hpp  
--- type_traits.original/detail/ice_not.hpp 2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/ice_not.hpp  2016-05-27 17:49:04.646825359 +1000  
@@ -14,7 +14,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (ice_not.hpp) is deprecated")  
+//("NOTE: Use of this header (ice_not.hpp) is deprecated")  
 #endif  
  
 namespace boost {  
diff -ru type_traits.original/detail/ice_or.hpp type_traits/detail/ice_or.hpp  
--- type_traits.original/detail/ice_or.hpp  2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/ice_or.hpp   2016-05-27 17:49:04.650825317 +1000  
@@ -14,7 +14,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (ice_or.hpp) is deprecated")  
+//("NOTE: Use of this header (ice_or.hpp) is deprecated")  
 #endif  
  
 namespace boost {  
diff -ru type_traits.original/detail/template_arity_spec.hpp type_traits/detail/template_arity_spec.hpp  
--- type_traits.original/detail/template_arity_spec.hpp 2016-05-27 17:45:56.292804600 +1000  
+++ type_traits/detail/template_arity_spec.hpp  2016-05-27 17:49:04.654825275 +1000  
@@ -10,7 +10,7 @@  
 // This header is deprecated and no longer used by type_traits:  
 //  
 #if defined(__GNUC__) || defined(_MSC_VER)  
-# pragma message("NOTE: Use of this header (template_arity_spec.hpp) is deprecated")  
+//("NOTE: Use of this header (template_arity_spec.hpp) is deprecated")  
 #endif  
  
 #   define BOOST_TT_AUX_TEMPLATE_ARITY_SPEC(i, name) /**/  
```

---

## Comment 5

> Username: Kojoley  
> Created_at: 2016-08-14 11:17:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-239668278  

Boost 1.62 will ship soon. Can we have this fix in it?

---

## Comment 6

> Username: nigels-com  
> Created_at: 2016-08-14 11:37:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-239669018  

Indeed, it's a good window of opportunity.

---

## Comment 7

> Username: ishitatsuyuki  
> Created_at: 2016-09-19 12:50:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-247984071  

Seems 71b4ed2 fixed this in a graceful way.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2016-09-19 16:13:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/25#issuecomment-248039394  

> Seems 71b4ed2 fixed this in a graceful way.  
  
No, it was fixed with #24 merge, thanks to @turkanis for this.

---
