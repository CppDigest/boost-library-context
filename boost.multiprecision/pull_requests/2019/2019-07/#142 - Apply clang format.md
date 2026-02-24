# #142 Apply clang format [Merged]

> Username: jzmaddock  
> Created at: 2019-07-15 17:26:20 UTC  
> Updated at: 2019-11-02 11:28:09 UTC  
> Merged at: 2019-08-02 19:24:41 UTC  
> Closed at: 2019-08-02 19:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-07-16 07:59:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142#issuecomment-511709536  

@nemo1369 I've hit a showstopper here: clang-format is mangling closing >'s in template declarations.  It's OK in C++11, but all the C++03 builds are broken.  This is despite putting  
  
```  
Standard: Cpp03  
```  
  
In the .clang-format file.  Any ideas anyone?

---

## Comment 2

> Username: nemothenoone  
> Created_at: 2019-07-16 11:28:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142#issuecomment-511777297  

@jzmaddock Is it ```SpaceAfterTemplateKeyword: false``` keyword you are looking for?

---

## Comment 3

> Username: pabristow  
> Created_at: 2019-07-16 11:56:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142#issuecomment-511785043  

No -it is  
  
"Standard (LanguageStandard)  
Format compatible with this standard, e.g. use A<A<int> > instead of A<A<int>> for LS_Cpp03.  
  
Possible values:  
  
LS_Cpp03 (in configuration: Cpp03) Use C++03-compatible syntax."  
  
Standard: Cpp03  
  
that doesn't work for John.  
  
Might it be   
  
Standard: LS_Cpp03 ??  
  
(We are trying to avoid gratuitously making Boost.Math and Boost.Multiprecision no longer compatible with C++03.  Although some people are forging ahead to the latest version, there are platforms, compilers and peoples existing configuration that cannot move on easily, if at all.  So while it is fine to introduce C++11 and above in new code, we should not do this unnecessarily).

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-07-16 12:27:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142#issuecomment-511794491  

>Is it SpaceAfterTemplateKeyword: false keyword you are looking for?  
  
No, it's changing some closing `> >` to `>>` even when the std version is set to 03.

---

## Comment 5

> Username: nemothenoone  
> Created_at: 2019-07-26 00:00:04 UTC  
> Updated_at: 2019-07-26 00:05:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/142#issuecomment-515257042  

Okay. Since I've found  no configuration adjustment solution, I suggest to wrap the only macro definition where Clang.Format fails (in ```defaullt_ops.hpp```) with ```// clang-format off``` and ```// clang-format on``` and apply manual formatting.  
  
Got it done in https://github.com/NilFoundation/multiprecision fork. I've made a new pull request for this pull request in #146 .

---
