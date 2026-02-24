# #5 Use __has_attribute(unused) macro as a check [Closed]

> Username: Houndie  
> Created at: 2015-04-13 19:31:48 UTC  
> Updated at: 2015-04-13 19:34:48 UTC  
> Closed at: 2015-04-13 19:34:48 UTC  
> Url: https://github.com/boostorg/concept_check/pull/5  

While currently the gcc marketing version number is used to check for  
the **attribute**((unused)) feature of the language, clang has its "gcc  
compatibility version number" set to 4.2, causing warnings to appear  
when compiling with clang and -Wunused-local-typedefs  
  
Using the __has_attribute(unused) macro will satisfy the clang  
compiler, and is also supported in gcc starting with 5.0

---
