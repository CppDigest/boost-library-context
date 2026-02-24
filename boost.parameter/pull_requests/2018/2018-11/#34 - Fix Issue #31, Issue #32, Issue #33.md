# #34  Fix Issue #31, Issue #32, Issue #33 [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-17 16:38:07 UTC  
> Updated at: 2018-11-20 13:26:39 UTC  
> Merged at: 2018-11-20 13:15:17 UTC  
> Closed at: 2018-11-20 13:15:17 UTC  
> Url: https://github.com/boostorg/parameter/pull/34  

- Change test/evaluate_category.cpp and test/preprocessor_eval_category.cpp so that the tests pass for msvc-8.0, msvc-9.0, msvc-10.0, and msvc-11.0.  
- Change test/Jamfile.v2 so that the compose_msvc11_fail_11 test runs only for msvc-11.0.  
- Ensure that file permissions for header and source files do not include execute.  
- Also, use the parameter_all_tests_cxx03 test suite vice the parameter_all_tests test suite when CXXSTD=03.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-11-17 17:39:25 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439634091  

Your jamfile change is saying that a number of aliases which have 'msvc' as part of their names should be run by all compilers. Is that what you intend ? In that case renaming the aliases without the 'msvc' part seems clearer.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-11-17 17:46:13 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439634599  

Okay, I’ll rename the aliases to make their purpose more clear.  
  
On Sat, Nov 17, 2018 at 12:39 Edward Diener <notifications@github.com>  
wrote:  
  
> Your jamfile change is saying that a number of aliases which have 'msvc'  
> as part of their names should be run by all compilers. Is that what you  
> intend ? In that case renaming the aliases without the 'msvc' part seems  
> clearer.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/parameter/pull/34#issuecomment-439634091>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsHh7cKtg1TJzaZLaCX5ePdHGRGGcks5uwEnOgaJpZM4YniBD>  
> .  
>

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-11-17 18:14:01 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439636703  

You are missing the point. Tests in the jamfile are often run as just 'b2 toolset=xxx' without specifying an alias name. In fact that is how the tests are run in the Boost regression test framework and how I run the tests locally. This has to work for all compilers. I understand that your CI tests run specific to certain aliases for certain tests, but that is not the canonical way of running the tests for regression testing or local testing.  
  
The tests you now specify as msvc12 etc. will be run when I use 'b2 toolset=gcc-8.1'. They need to pass no matter what compiler I use, since you removed the msvc requirement on those tests. Therefore naming them msvc12, or even msvcxxx is a misleading name for them. It does not bother me if you want to keep those names, just as long as those tests run correctly for all compilers.

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2018-11-17 18:29:50 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439637741  

Ah, that's what you meant.  Okay, I'll try to rearrange the aliases and reinstate the msvc requirements so that .travis.yml and appveyor.yml won't need to specify an alias name.  I'll bring back test-suite statements if I have to.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-11-17 18:49:17 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439638998  

You can continue to use alias names for travis and appveyor. I am just saying that regression testing and normal local testing does not use alias names. However there is nothing wrong with invoking tests using alias names itself. In the doc you can explain the various testing procedures which can be used with alias names. But you still need to have the jamfile work correctly when no alias names are used.

---

## Comment 6

> Username: eldiener  
> Created_at: 2018-11-18 10:48:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439683406  

I also wanted to mention that if you specify 'explicit somealias ;' in a jamfile, then that alias does not get processed when you run b2 against the jamfile specifying no alias names; otherwise all aliases get processed when specifying no alias names. You might want to use that technique in your design of the Parameter test jamfile.

---

## Comment 7

> Username: CromwellEnage  
> Created_at: 2018-11-19 13:50:21 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-439898863  

I've rearranged most of the aliases so they're still useful.  Nevertheless, I brought back the test-suite statement, so I don't think I need 'explicit [some_alias]'.  (Thanks for the tip, though.)

---

## Comment 8

> Username: eldiener  
> Created_at: 2018-11-19 19:12:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440008142  

Why did you remove the jamfile code which specifies that the python test fails for gcc on Windows ?

---

## Comment 9

> Username: CromwellEnage  
> Created_at: 2018-11-19 19:17:39 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440009694  

Because they did’t fail when address-model was set to 32 bits.  But if  
mingw compiler configurations are expected to fail on python tests  
regardless of address-model, then I’ll put that back.  
  
On Mon, Nov 19, 2018 at 14:12 Edward Diener <notifications@github.com>  
wrote:  
  
> Why did you remove the jamfile code which specifies that the python test  
> fails for gcc on Windows ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/parameter/pull/34#issuecomment-440008142>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsDChCJITAFORgDns9IPoK7QqMkV_ks5uwwKegaJpZM4YniBD>  
> .  
>

---

## Comment 10

> Username: eldiener  
> Created_at: 2018-11-19 19:40:52 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440016990  

My testing show that there is failure when the address model is set to 32-bits and the mode is c++11 on up; only when the mode is c++03 mode does it succeed. But since c++03 mode will hardly be used by programmers I would rather we just say that it always fails with gcc under Windows. This is not the fault of your code but is the fault instead of a low-level Python header. See https://github.com/boostorg/python/issues/140 and https://github.com/python/cpython/pull/880.

---

## Comment 11

> Username: eldiener  
> Created_at: 2018-11-19 23:12:31 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440076184  

When you have more than one test of the same name, such as 'evaluate_category.cpp' they need a different final name to distinguish them. The default final name, if you do not choose to give one, is the test name minus the extension, so that the default final name for 'evaluate_category.cpp'  is 'evaluate_category'  but you can always of course specify the final name you want. But if you have more than one of the same final names, even if they are in different aliases, you will get the error:  
  
error: No best alternative for ./evaluate_category_msvc_fail  
    next alternative: required properties: (empty)  
        matched  
    next alternative: required properties: (empty)  
        matched  
  
etc. The same thing goes for preprocessor_eval_category.cpp in your jamfile. So you need to correct this in your jamfile.v2 file.

---

## Comment 12

> Username: CromwellEnage  
> Created_at: 2018-11-20 03:10:13 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440121823  

I noticed that the error shows up even for targets without any tests.  What should I do about those?

---

## Comment 13

> Username: eldiener  
> Created_at: 2018-11-20 04:55:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440137996  

Please givew me an example of the error showing for targets without any tests ?

---

## Comment 14

> Username: CromwellEnage  
> Created_at: 2018-11-20 06:12:01 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440156421  

For Job 3 (g++-4.7), the first error reads:  
  
error: No best alternative for ./parameter_macros_eval_category  
    next alternative: required properties: <target-os>linux <toolset>gcc  
        matched  
    next alternative: required properties: <cxxstd>03 <target-os>darwin  
        not matched  
    next alternative: required properties: <target-os>windows <toolset>gcc  
        not matched  
    next alternative: required properties: <cxxstd>98  
        not matched  
    next alternative: required properties: <cxxstd>03  
        matched  
  
The job ends up picking the first alternative in this case, which is the desired outcome here.

---

## Comment 15

> Username: CromwellEnage  
> Created_at: 2018-11-20 06:22:45 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440158370  

On further inspection, it looks like my use of <version> in those test statements is being ignored.  I had the same problem trying to turn off tests for specific versions of Xcode.  I'm at a roadblock here.

---

## Comment 16

> Username: CromwellEnage  
> Created_at: 2018-11-20 06:53:20 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440164145  

Okay, I figured it out, at least for gcc.  I didn't find any Jamfile errors in the test results off my fork.

---

## Comment 17

> Username: eldiener  
> Created_at: 2018-11-20 13:15:08 UTC  
> Updated_at: 2018-11-20 13:16:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/34#issuecomment-440269247  

Yes \<version\> is really not a separate property. It needs to always be \<toolset-somecompiler:version\>nnn. I am glad you figured that out.

---
