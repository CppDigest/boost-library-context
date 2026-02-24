# #6 Use __has_attribute(unused) macro as a check [Closed]

> Username: Houndie  
> Created at: 2015-04-13 19:36:56 UTC  
> Updated at: 2015-04-29 21:03:18 UTC  
> Closed at: 2015-04-29 21:03:18 UTC  
> Url: https://github.com/boostorg/concept_check/pull/6  

While currently the gcc marketing version number is used to check for  
the **attribute**((unused)) feature of the language, clang has its "gcc  
compatibility version number" set to 4.2, causing warnings to appear  
when compiling with clang and -Wunused-local-typedefs  
  
Using the __has_attribute(unused) macro will satisfy the clang  
compiler, and is also supported in gcc starting with 5.0

---

## Comment 1

> Username: Houndie  
> Created_at: 2015-04-13 19:39:48 UTC  
> Url: https://github.com/boostorg/concept_check/pull/6#issuecomment-92473808  

This is a copy of https://github.com/boostorg/concept_check/pull/4 and https://github.com/boostorg/concept_check/pull/5, except apparently I'm having a bad day today, and submitting buggy code for a merge.  Twice.  
  
Apparently because __has_attribute is a macro, it's being evaluated before the defined(__has_attribute) statement is short circuiting the expression.  You learn something new every day, I guess.  Splitting the code into separate if blocks (as demonstrated by the gcc 5.0 example in the change log, no less), makes older versions of gcc happy.

---

## Comment 2

> Username: Houndie  
> Created_at: 2015-04-29 20:15:34 UTC  
> Url: https://github.com/boostorg/concept_check/pull/6#issuecomment-97570884  

Note:  Someone else submitted https://github.com/boostorg/concept_check/pull/7 which seems cleaner than my patch, so I would recommend using that one.

---
