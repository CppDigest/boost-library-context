# #642 Hypergeomkurtosis [Merged]

> Username: pabristow  
> Created at: 2021-06-16 13:23:24 UTC  
> Updated at: 2021-06-26 11:25:59 UTC  
> Merged at: 2021-06-26 11:24:23 UTC  
> Closed at: 2021-06-26 11:24:23 UTC  
> Url: https://github.com/boostorg/math/pull/642  

In response to https://github.com/boostorg/math/issues/639 I've changed the expression to calculate the hypergeometric distribution kurtosis excess to use Wolfram Alpha version and confirmed that it agrees with a separate implementation of Wikipedia formula and agrees with Python scipy as well.  Used boost::multiprecision::cpp_bin_float_quad in a separate test.  
  
Updated tests for moments using Wolfram values. Passes local tests msvc, gcc and clang.   
  
Matching changes to documentation, but unable to update equations as don't have MathML software (and adding the formula for kurtosis is tedious and error-prone so replace by a reference to Wikipedia and Wolfram).  
  
Docs rebuilt and running boost.Inspect reveals no broken links.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-06-26 09:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/642#issuecomment-868977383  

That all looks fine, merging...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-06-26 09:51:07 UTC  
> Url: https://github.com/boostorg/math/pull/642#issuecomment-868977527  

Ah... not merging after all, it's marked as draft, so you will have to do it.

---

## Comment 3

> Username: pabristow  
> Created_at: 2021-06-26 11:25:59 UTC  
> Url: https://github.com/boostorg/math/pull/642#issuecomment-868987518  

Sorry - I didn't realise the implications of being marking as draft.  
  
Merged OK.

---
