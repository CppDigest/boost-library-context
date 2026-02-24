# #13 Mark the IBM XL compiler as supported [Closed]

> Username: theZiz  
> Created at: 2018-02-15 07:38:57 UTC  
> Updated at: 2018-02-19 09:48:30 UTC  
> Closed at: 2018-02-18 14:53:25 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13  

Tested with the same code as in https://svn.boost.org/trac10/ticket/13447 respectively https://github.com/boostorg/preprocessor/pull/12  
  
I don't know which version of the XL compiler is the first where variadic macros are working (properly). I only tested the most recent version 14.01. This [documentation](https://www.ibm.com/support/knowledgecenter/en/SSXVZZ_10.1.0/com.ibm.xlcpp101.linux.doc/language_ref/define.html) however states that it already worked with XL 10.1. I were not able to find an older documentation for support.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-02-15 15:28:31 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-365961689  

I am loth to do this unless I know that all versions support variadic macros. Would it not be enough to   
just remove:  
  
`defined __IBMCPP__`  
  
from its line and let the rest of the logic kick in ?

---

## Comment 2

> Username: trudeaun  
> Created_at: 2018-02-15 16:54:32 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-365990661  

@theZiz Hi Alex. The latest version of IBM XL C/C++ for Linux is V13.1.6, which was made available in December 2017. The oldest version of the compiler in service is IBM XL C/C++ for Linux V11.1. V10.1 hit end of support in 2015. Variadic macros are supported for all IBM XL C/C++ for Linux compiler versions in service. Are you using any compilers that are out of service? Please see [here ](https://www-01.ibm.com/software/support/lifecycleapp/PLCSearch.wss?q=xl+c+for+linux&ibm-search=Search) for more details.

---

## Comment 3

> Username: theZiz  
> Created_at: 2018-02-16 08:39:07 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-366174352  

@trudeaun Well, I am on an IBM test machine, I didn't notice, but the compiler is a beta:  
```  
bash-4.2$ xlc --version  
IBM XL C/C++ for Linux, V14.1 (Beta 2)  
Version: 14.01.0000.0000  
```  
Unfortunately no other version of the compiler is available on the system. However as stated in the documentation, in that case every still supported compiler version should support variadic macros. :smiley:   
  
@eldiener: Well, I more or less copied the way to do this from https://github.com/boostorg/preprocessor/pull/12 . However, you are right, this would be a working solution, too. I just tested  
```C++  
#if !defined __cplusplus && __STDC_VERSION__ >= 199901L || __cplusplus >= 201103L  
#    define BOOST_PP_VARIADICS 1  
#endif  
```  
with my code and it works. :wink:   
  
Shall I close this PR and make a new one with the suggested better solution?

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-02-17 01:26:12 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-366403690  

You can just correct this PR rather than make a new one. If defaulting to the "#elif !defined __cplusplus && __STDC_VERSION__ >= 199901L || __cplusplus >= 201103L", test works we should use that.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-02-18 14:52:49 UTC  
> Updated_at: 2018-02-18 14:53:18 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-366521542  

I made the change on the 'develop' branch so that the IBM compilers defaults to the level of compliance based on the C++/C version macros as far as variadic macro support is concerned.

---

## Comment 6

> Username: theZiz  
> Created_at: 2018-02-19 09:48:30 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/13#issuecomment-366637659  

Thanks! :smiley:

---
