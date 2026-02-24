# #193 - Update for 15.5 preview [Closed]

> Username: danieljames  
> Created at: 2017-11-17 10:22:03 UTC  
> Updated at: 2017-12-09 18:10:39 UTC  
> Closed at: 2017-12-09 18:10:39 UTC  
> Url: https://github.com/boostorg/config/issues/193  

This was discussed here:  
  
http://boost.2283326.n4.nabble.com/MSVC-15-5-support-td4699334.html  
  
And has been requested for boost 1.66.0 as msvc 15.5 should be released about the same time.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-11-17 12:19:15 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-345229562  

If we're signalling that version 1920 is supported, I'd like to know what, if any new features have been added since this appears to be a new compiler version (unlike msvc 15.4 which keeps the compiler version at 1910).

---

## Comment 2

> Username: danieljames  
> Created at: 2017-11-17 12:27:58 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-345231268  

I'm not sure, I created this to flag up the issue in case you hadn't noticed. But there are some [release notes](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-preview-relnotes).

---

## Comment 3

> Username: OlafvdSpek  
> Created at: 2017-11-17 12:32:11 UTC  
> Updated at: 2017-11-17 12:33:44 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-345232119  

@jzmaddock 15.3 is 1911 isn't it?   
15.5 is 1912  
  
https://blogs.msdn.microsoft.com/vcblog/2017/11/15/msvc-conformance-improvements-in-visual-studio-2017-version-15-5/

---

## Comment 4

> Username: OlafvdSpek  
> Created at: 2017-12-02 09:03:52 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-348678836  

@jzmaddock Can this be done for 1.66 or is it too late?

---

## Comment 5

> Username: egorpugin  
> Created at: 2017-12-05 13:00:41 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-349297289  

VS15.5 is out.  
+1 to previous question.

---

## Comment 6

> Username: danieljames  
> Created at: 2017-12-05 14:04:45 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-349313391  

We're closing for release tomorrow, so we'd probably have to delay the release to handle this. It can have knock on effects for other libraries.

---

## Comment 7

> Username: egorpugin  
> Created at: 2017-12-05 14:16:22 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-349316565  

True. You probably need some policy for handling such situations.  
Possible solution is to ship VS15.5 fixes into 1.66.1 as it was for 1.65.1.  
  
Now I see tons of warnings in C++17 mode in VS15.5 on current master.

---

## Comment 8

> Username: OlafvdSpek  
> Created at: 2017-12-05 14:21:51 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-349318090  

Taking action sooner would help a lot too..

---

## Comment 9

> Username: pdimov  
> Created at: 2017-12-07 22:11:57 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-350110392  

Results of the Config tests for 15.5:  
  
```  
C:\Projects\boost-git\boost\libs\config\test\all>b2 toolset=msvc-14.1 | grep \.\  
.\.failed  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx11_sfinae_  
expr_fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\no_cxx11_sfinae_e  
xpr_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_ded_typename_  
fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\no_ded_typename_fail.o  
bj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_two_phase_loo  
kup_fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\no_two_phase_looku  
p_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_macro_use_fa  
cet_fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\has_macro_use_face  
t_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_nrvo_fail.te  
st\msvc-14.1\debug\threadapi-win32\threading-multi\has_nrvo_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_com_value_ini  
t_fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\no_com_value_init_fa  
il.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx11_hdr_cod  
ecvt_fail.test\msvc-14.1\debug\threadapi-win32\threading-multi\no_cxx11_hdr_code  
cvt_fail.obj...  
...failed updating 7 targets...  
```

---

## Comment 10

> Username: pdimov  
> Created at: 2017-12-07 22:12:18 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-350110477  

Same in C++17 mode:  
  
```  
C:\Projects\boost-git\boost\libs\config\test\all>b2 toolset=msvc-14.1 cxxstd=17  
| grep \.\.\.failed  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx11_sfinae_  
expr_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\no_  
cxx11_sfinae_expr_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx17_fold_ex  
pressions_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-mult  
i\no_cxx17_fold_expressions_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx17_inline_  
variables_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-mult  
i\no_cxx17_inline_variables_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_ded_typename_  
fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\no_ded_t  
ypename_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_two_phase_loo  
kup_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\no_t  
wo_phase_lookup_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_macro_use_fa  
cet_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\has_  
macro_use_facet_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_nrvo_fail.te  
st\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\has_nrvo_fail.o  
bj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_com_value_ini  
t_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\no_com  
_value_init_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx11_hdr_cod  
ecvt_fail.test\msvc-14.1\debug\cxxstd-17-iso\threadapi-win32\threading-multi\no_  
cxx11_hdr_codecvt_fail.obj...  
...failed updating 9 targets...  
```

---

## Comment 11

> Username: pdimov  
> Created at: 2017-12-07 22:18:27 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-350111864  

For reference, here's msvc-14.0 on the same tests:  
```  
C:\Projects\boost-git\boost\libs\config\test\all>b2 toolset=msvc-14.0 | grep \.\  
.\.failed  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_ded_typename_  
fail.test\msvc-14.0\debug\threadapi-win32\threading-multi\no_ded_typename_fail.o  
bj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_two_phase_loo  
kup_fail.test\msvc-14.0\debug\threadapi-win32\threading-multi\no_two_phase_looku  
p_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_macro_use_fa  
cet_fail.test\msvc-14.0\debug\threadapi-win32\threading-multi\has_macro_use_face  
t_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\has_nrvo_fail.te  
st\msvc-14.0\debug\threadapi-win32\threading-multi\has_nrvo_fail.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_com_value_ini  
t_fail.test\msvc-14.0\debug\threadapi-win32\threading-multi\no_com_value_init_fa  
il.obj...  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\config\test\all\no_cxx11_hdr_cod  
ecvt_fail.test\msvc-14.0\debug\threadapi-win32\threading-multi\no_cxx11_hdr_code  
cvt_fail.obj...  
...failed updating 6 targets...  
```

---

## Comment 12

> Username: jzmaddock  
> Created at: 2017-12-09 18:10:39 UTC  
> Url: https://github.com/boostorg/config/issues/193#issuecomment-350495066  

Please see: https://github.com/boostorg/config/pull/198
