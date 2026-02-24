# #170 Add custom `boost::locale::numpunct` [Open]

> Username: Flamefire  
> Created at: 2023-05-21 10:23:49 UTC  
> Updated at: 2024-08-16 11:32:19 UTC  
> Url: https://github.com/boostorg/locale/pull/170  

Rebase of #65 onto develop with current changes incorporated. Hence closes #65  
  
Contains the original commit(s) from @salvoilmiosi of #65 squashed and rebased onto develop to fix the conflicts.     
On top some refactoring and documentation additions to suit current state of the library.  
  
Will also contain tests.  
  
Design:  
- provide `boost::locale::numpunct` for all backends  
- add `decimal_point_str` & `thousands_sep_str` to provide possibly encoded variants of the decimal and thousand separators  
-  `decimal_point` & `thousands_sep` return best-effort single-chars, by default falling back to the "C" locale  
- Tests should ensure that a Boost.Locale agnostic library (i.e. reading `facet.thousands_sep()` & `facet.decimal_point()` and doing "regular" streaming using `std::locale`) get the expected results.

---
