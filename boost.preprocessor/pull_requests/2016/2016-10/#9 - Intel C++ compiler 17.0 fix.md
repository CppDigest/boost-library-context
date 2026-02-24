# #9 Intel C++ compiler 17.0 fix [Merged]

> Username: sergiud  
> Created at: 2016-10-23 17:44:57 UTC  
> Updated at: 2016-11-17 12:33:18 UTC  
> Merged at: 2016-11-16 14:40:22 UTC  
> Closed at: 2016-11-16 14:40:22 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9  

This PR adds a workaround for Intel C++ compiler 17.0 and MSVC. Without these changes, Boost.Accumulators and Boost.Test cause hundreds of compilation errors when compiling a project that uses both libraries.

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-10-27 17:53:03 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256720431  

Does the Intel compiler really define _MSC_VER ?   
  
I ask this because if the Intel compiler defines _MSC_VER it probably triggers a huge amount of workarounds in Boost PP which must be done because the VC++ preprocessor is non-standard in many ways.

---

## Comment 2

> Username: sergiud  
> Created_at: 2016-10-27 18:08:08 UTC  
> Updated_at: 2016-10-27 18:08:44 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256724186  

It does which is however fine, because the Intel compiler works in MSVC emulation mode on Windows.  
  
These are all the macros defined by Intel C++ compiler 17.0:  
  
``` c++  
#define __SIGNED_CHARS__ 1  
#define __DATE__ "Oct 24 2016"  
#define __TIME__ "23:21:40"  
#define __cilk 200  
#define __cplusplus 199711L  
#define __STDC_HOSTED__ 1  
#define _WCHAR_T 1  
#define _WCHAR_T_DEFINED 1  
#define _NATIVE_WCHAR_T_DEFINED 1  
#define __CHAR16_T_AND_CHAR32_T 1  
#define _HAS_CHAR16_T_LANGUAGE_SUPPORT 1  
#define __BOOL_DEFINED 1  
#define _NATIVE_NULLPTR_SUPPORTED 1  
#define __ARRAY_OPERATORS 1  
#define __RTTI 1  
#define __cpp_unicode_characters 200704  
#define __cpp_sized_deallocation 201309  
#define __cpp_attributes 200809  
#define __cpp_binary_literals 201304  
#define __cpp_decltype 200707  
#define __cpp_decltype_auto 201304  
#define __cpp_generic_lambdas 201304  
#define __cpp_init_captures 201304  
#define __cpp_lambdas 200907  
#define __cpp_raw_strings 200710  
#define __cpp_return_type_deduction 201304  
#define __cpp_rtti 199711  
#define __cpp_rvalue_references 200610  
#define __cpp_static_assert 200410  
#define __cpp_unicode_literals 200710  
#define __cpp_user_defined_literals 200809  
#define __cpp_variadic_templates 200704  
#define __cpp_constexpr 200704  
#define __LONG_DOUBLE_SIZE__ 64  
#define __VARIADIC_TEMPLATES 1  
#define __STDCPP_THREADS__ 1  
#define __EDG_CONSTEXPR_ENABLED__ 1  
#define __EDG__ 1  
#define __EDG_VERSION__ 411  
#define __EDG_SIZE_TYPE__ unsigned int  
#define __EDG_PTRDIFF_TYPE__ int  
#define __DEC32_MANT_DIG__ 0  
#define __DEC64_MANT_DIG__ 0  
#define __DEC128_MANT_DIG__ 0  
#define __DEC32_MIN_EXP__ 0  
#define __DEC64_MIN_EXP__ 0  
#define __DEC128_MIN_EXP__ 0  
#define __DEC32_MAX_EXP__ 0  
#define __DEC64_MAX_EXP__ 0  
#define __DEC128_MAX_EXP__ 0  
#define __ICL 1700  
#define __INTEL_COMPILER 1700  
#define __INTEL_COMPILER_UPDATE 0  
#define _USE_ATTRIBUTES_FOR_SAL 0  
#define SAL_NO_ATTRIBUTE_DECLARATIONS 1  
#define _INTEGRAL_MAX_BITS 64  
#define _CPPRTTI 1  
#define __QMSPP_ 1  
#define _WIN32 1  
#define __w64  
#define _M_IX86 700  
#define _MSC_VER 1900  
#define _MSC_FULL_VER 190024215  
#define _MSC_EXTENSIONS 1  
#define _MT 1  
#define __INTEL_COMPILER_BUILD_DATE 20160721  
#define __INTEL_OFFLOAD 1  
#define __SSE2__ 1  
#define __SSE2_MATH__ 1  
#define __SSE__ 1  
#define __SSE_MATH__ 1  
#define __MMX__ 1  
#define __INTEL_MS_COMPAT_LEVEL 1  
```  
  
I use a couple of Boost libraries that heavily rely on Boost.Preprocessor, e.g., Boost.Accumulators, Boost.Test and Boost.ScopeExit. Only the two workarounds from the PR were needed to resolve compilation issues.

---

## Comment 3

> Username: eldiener  
> Created_at: 2016-10-27 19:07:08 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256739319  

I appreciate the list of defines.  
  
I have a few comments:  
  
1) Emulating the non-standard VC++ preprocessor on Windows is not something I would wish on anybody. The fact that you want some changes made already indicates that the "emulation" does not work in all respects, ie that the emulation is slightly broken. Having worked extensively with the non-standard VC++ preprocessor on Windows in programming Boost PP, I know how difficult it is to understand what it is doing in any given situation.  
  
2) The workarounds will also affect other libraries, so while they may fix problems in the libraries in which you work with Intel C++ 17 on Windows there is no guarantee that they will not break other Boost libraries when using Intel C++ 17 on Windows.  
  
3) I do not test Intel C++ on Windows because I would have to buy a license for it in order to test it. I can test Intel C++ on Linux because Intel allows Boost developers access to it as a free version. So I am in a real quandary about approving this PR, because without testing all Boost libraries which use Boost PP with Intel C++ on Windows I cannot know if your PR will break something else or not.

---

## Comment 4

> Username: sergiud  
> Created_at: 2016-10-27 19:16:45 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256741882  

I understand. However, the changes do only affect Intel >= 17.0 with MSVC. The probability that something breaks is very small.  
  
I can of course run the tests myself and report the results. I'm also open for other suggestions as well.

---

## Comment 5

> Username: eldiener  
> Created_at: 2016-10-27 19:38:37 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256747311  

I agree that the change only affects  Intel >= 17.0 on Windows. However the 'probability that something breaks is very small' can hardly be ascertained in advance. In both cases you are making major chances to how the compiler works in Boost PP. I am sure you know that BOOST_PP_CAT is  a very useful macro for developers. As for turning on variadic macro support for Intel C++ 17, even if this is always the case no matter what level of c++ standard support is occurring during compilation, you change is also setting a macro which specifies particular support for VC++ variadic macro programming. If you think VC++ is non-conforming in its normal use of the preprocessor it is even  more non-conforming when it comes to variadic macros.  
  
If there is any chance you could test all libraries with Intel C++ 17 on Windows of course I would like you to do so, but you might have to compare the outcome with and without your PR to see if the PR breaks anything. That is a really big task to do.

---

## Comment 6

> Username: sergiud  
> Created_at: 2016-10-28 10:13:35 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-256885474  

Let me try it. This will take some time though.

---

## Comment 7

> Username: eldiener  
> Created_at: 2016-10-29 16:29:14 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-257101214  

If you could do this it will be beneficial because I have no way of testing whether or not your change works correctly. If I merge your pull request, and there are any problems reported to me by developers using the latest Intel C++ 17 on Windows using Boost libraries which use Boost PP or even using Boost PP itself in their own code, I would have no recourse but to back out the pull request. This is unfortunate because Intel does not offer any free version of Intel C++ on Windows for developers whose testing can discover bugs in their product, whereas they do on Linux.

---

## Comment 8

> Username: sergiud  
> Created_at: 2016-10-29 19:43:16 UTC  
> Updated_at: 2016-10-30 10:49:38 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-257111773  

I actually don't understand your argument. Intel 17.0 on Windows with Boost is broken anyway. So, it's not like this PR will break Boost even more. The PR does actually fix problems with several libraries.  
  
And there's a free version of Intel compiler for all platforms for academic use. This is the one I use.  
  
Also, it took me a while the find and fix problem. It would be a shame if other Boost users do not benefit from this. Especially since Boost is obviously not tested with the Intel compiler on Windows as with other mainstream compilers.

---

## Comment 9

> Username: eldiener  
> Created_at: 2016-10-29 21:45:03 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-257117819  

I do not know whether Intel 17.0 on Windows with Boost is broken because I cannot test it and nobody has been willing to upload Boost regression tests for it. I will take your word for it that it is broken in the Boost libraries in which you use it.  
  
I am not in college so I cannot obtain the free version for Windows. If I could obtain Intel C++ on Windows for free, purely to test Boost and not to use it commercially in any way, believe me I would. I already test many versions of clang, gcc, and VC++ on Windows, since I prefer Windows to Linux and am used to working in it, so adding tests for Intel C++ on Windows would be no burden for me.  
  
I appreciate the work you did to find and fix the problem. I really do. My problem is that I cannot know what the result of your change does when running Intel C++ and using Boost PP. That is of course not your problem but you can understand why blindly accepting your fix without knowing whether it works or not for whatever Boost libraries use Boost PP bothers me. Furthermore because Intel is yet another compiler which has decided to emulate the essentially non-standard VC++ preprocessor on Windows ( clang targeting VC++ has also gone that route ), and isn't doing that correctly as is, I am loath to add a workaround for that situation. Nonetheless I will merge your pull request with the proviso that if I hear of another user telling me that it breaks some other usage of Intel C++ 17 and Boost PP, whether with another Boost library or if that person uses Boost PP directly, I am going to back out the PR. Naturally I hope that does not happen.

---

## Comment 10

> Username: eldiener  
> Created_at: 2016-10-29 21:51:02 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-257118091  

I am sorry I did not notice it until now but you will need to make this PR against the 'develop' branch and not the 'master' branch. No PRs are ever accepted against the 'master' branch of a Boost library. Once a 'develop' branch PR is merged, if the regression tests show that the change is OK, then it gets merged to the 'master' branch. Please make the same PR against the latest 'develop' branch and I will either merge it to the 'develop' branch or cherry-pick it to the 'develop' branch.

---

## Comment 11

> Username: eldiener  
> Created_at: 2016-11-11 13:52:28 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-259961344  

I have been able to get a free license for Intel-17.0 for Windows so I can now test changes.

---

## Comment 12

> Username: sergiud  
> Created_at: 2016-11-11 13:58:07 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-259962293  

Great, thank you! Rebasing the patch to develop should not be a problem.

---

## Comment 13

> Username: eldiener  
> Created_at: 2016-11-11 14:18:32 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-259966353  

OK, please submit another PR based on the 'develop' branch of PP. I can then test it to make sure it is working properly for all Boost libraries including those in which you are currently seeing problems.

---

## Comment 14

> Username: sergiud  
> Created_at: 2016-11-11 14:34:43 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-259969602  

I've updated the PR. I hope that's okay.

---

## Comment 15

> Username: eldiener  
> Created_at: 2016-11-11 14:42:34 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-259971210  

That is fine. Let me test and if I see your fix is not causing problems with Intel 17 I will merge the pull request. This make take awhile so be patient, as I am not sure when I can get around to testing this.

---

## Comment 16

> Username: eldiener  
> Created_at: 2016-11-13 21:25:17 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-260214021  

With your changes the preprocessor tests do not pass for Intel C++ 17.0 on Windows. Without your changes the preprocessor tests do pass for Intel C++ 17.0 on Windows. So although your changes may solve problems for you they break preprocessor tests. I think part of the reason for this may be that variadic macro support is not turned on for Intel C++ 17.0 on Windows. But since your changes break the tests for Intel C++ 17.0 on Windows I cannot merge them as a general fix.  
  
I will look further and try to determine why your changes break the preprocessor tests. I am not sure whether or not Intel C++ 17.0 on Windows is supposed to emulate the non-standard VC++ preprocessor on Windows or not. If it is then even if I drop the BOOST_PP_CAT change and keep the config.hpp change, which completes the VC++ emulation for Intel C++ 17.0 on Windows, the preprocessor tests do not pass. I will try to determine why the tests do not pass and report problems to Intel when I do.

---

## Comment 17

> Username: eldiener  
> Created_at: 2016-11-16 14:40:07 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-260963708  

I have solved the problems with Intel C++ 17.0 on Windows and will merge this pull request. Please test the develop branch after this merge is made to see if your own problems are resolved.

---

## Comment 18

> Username: sergiud  
> Created_at: 2016-11-16 15:14:36 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-260970901  

Thank you! Will do and report back.

---

## Comment 19

> Username: sergiud  
> Created_at: 2016-11-17 12:33:18 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/9#issuecomment-261235649  

After fixing unrelated problems in Boost.ScopeExit and Boost.Typeof, I was able to compile my project without errors. Thanks!

---
