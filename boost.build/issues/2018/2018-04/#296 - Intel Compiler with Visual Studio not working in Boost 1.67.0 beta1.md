# #296 - Intel Compiler with Visual Studio not working in Boost 1.67.0 beta1 [Open]

> Username: danieljames  
> Created at: 2018-04-07 09:50:20 UTC  
> Updated at: 2021-06-26 03:10:15 UTC  
> Url: https://github.com/boostorg/build/issues/296  

From the development list:  
  
> Maybe it's too late for the fix but building of Boost 1.67.0 Beta 1 with  
> Visual Studio 2017 15.6.4 and Intel Compiler 18.0.2 doesn't work properly:  
>   
>     C:\nask\boost_1_67_0_b1_rc2.comp>b2 address-model=64 architecture=x86  
>     --build-dir=C:\windir\boost167intel.comp  
>     --stagedir=C:\windir\boost167intel.comp --build-type=complete  
>     --with-iostreams -s BZIP2_SOURCE=C:\nask\bzip2-1.0.6 -s  
>     ZLIB_SOURCE=C:\nask\zlib-1.2.8 variant=release link=shared link=static  
>     threading=multi runtime-link=shared runtime-link=static  
>     --toolset=intel-18.0-vc14.1  
>     cflags="/QaxCORE-AVX512,CORE-AVX2,AVX,SSE4.2,SSE4.1 /Qfma -O3 /Qip  
>     /Qopt-zmm-usage=high /fp:precise /fp:source /Qimf-arch-consistency:true  
>     /Qpc80 -wd10120" cxxflags="/QaxCORE-AVX512,CORE-AVX2,AVX,SSE4.2,SSE4.1 /Qfma  
>     -O3 /Qip /Qopt-zmm-usage=high /fp:precise /fp:source  
>     /Qimf-arch-consistency:true /Qpc80 /Qstd=c++14 -wd10120" -j4 --hash  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/tools\intel-win.jam:237: in  
>     configure-really  
>     ERROR: rule "msvc.maybe-rewrite-setup" unknown in module "intel-win".  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/tools\intel-win.jam:99: in  
>     configure  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/tools\intel-win.jam:46: in  
>     intel-win.init  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/build\toolset.jam:44: in  
>     toolset.using  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/tools\intel.jam:32: in  
>     intel.init  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src/build\toolset.jam:44: in  
>     toolset.using  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src\build-system.jam:543: in  
>     process-explicit-toolset-requests  
>     C:/nask/boost_1_67_0_b1_rc2.comp/tools/build/src\build-system.jam:610: in  
>     load  
>     C:\nask\boost_1_67_0_b1_rc2.comp\tools\build\src/kernel\modules.jam:295: in  
>     import  
>     C:\nask\boost_1_67_0_b1_rc2.comp\tools\build\src/kernel/bootstrap.jam:139:  
>     in boost-build  
>     C:\nask\boost_1_67_0_b1_rc2.comp\boost-build.jam:17: in module scope  
>   
> It seems that /intel-win.jam/ file is outdated in Boost 1.67.0 Beta 1 since  
> the /maybe-rewrite-setup/ rule has been removed from the /msvc.jam/ file.

---

## Comment 1

> Username: danieljames  
> Created at: 2018-04-07 12:26:39 UTC  
> Url: https://github.com/boostorg/build/issues/296#issuecomment-379465754  

More from mailing list, this patch looks like it should be applied?  
  
> I forgot to mention that I am using the following patch for the intel-win.jam  
> file:  
>   
>     diff -Nru boost_1_67_0_b1_rc1.orig/tools/build/src/tools/intel-win.jam  
>     boost_1_67_0_b1_rc1/tools/build/src/tools/intel-win.jam  
>     --- boost_1_67_0_b1_rc1.orig/tools/build/src/tools/intel-win.jam  
>     2018-03-08 14:07:09.000000000 +0100  
>     +++ boost_1_67_0_b1_rc1/tools/build/src/tools/intel-win.jam     2018-03-12  
>     14:46:07.484225270 +0100  
>     @@ -468,14 +468,14 @@  
>          <architecture>/<address-model>64  
>          <architecture>x86/<address-model>64 ;  
>   
>     -.intel-autodetect-versions = 14.0 13.0 12.0 ;  
>     +.intel-autodetect-versions = 14.1 14.0 13.0 12.0 ;  
>      .iclvars-12.0-supported-vcs = "10.0 9.0 8.0" ;  
>      .iclvars-12.1-supported-vcs = "10.0 9.0 8.0" ;  
>      .iclvars-13.0-supported-vcs = "11.0 10.0 9.0" ;  
>      .iclvars-14.0-supported-vcs = "12.0 11.0 10.0 9.0" ;  
>      .iclvars-15.0-supported-vcs = "12.0 11.0 10.0 9.0" ;  
>      .iclvars-16.0-supported-vcs = "14.0 12.0 11.0 10.0 9.0" ;  
>     -.iclvars-17.0-supported-vcs = "14.0 12.0 11.0 10.0" ;  
>     +.iclvars-17.0-supported-vcs = "14.1 14.0 12.0 11.0 10.0" ;  
>      .iclvars-18.0-supported-vcs = "14.1 14.0 12.0 11.0 10.0" ;  
>      .iclvars-version-alias-vc14.1 = vs2017 ;  
>      .iclvars-version-alias-vc14 = vs2015 ;  
>   
> My approach has been working well for all Boost 1.6x versions except the  
> recent one (1.67 Beta) - probably because of the latest changes in the  
> msvc.jam file.  
>   
> Before b2, I am executing bootstrap.bat command in the root Boost folder.

---

## Comment 2

> Username: sav-ix  
> Created at: 2018-04-11 18:20:54 UTC  
> Url: https://github.com/boostorg/build/issues/296#issuecomment-380548680  

Hello, everyone,  
  
Got he same error `rule "msvc.maybe-rewrite-setup" unknown in module "intel-win"` for buils using Windows ICC 2018. And with PR# 51f9a4c338924a53092e150ee237fc3adfa43b91 applied error not reproduced.  
@swatanabe, thank you for fix!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:33 UTC  
> Url: https://github.com/boostorg/build/issues/296#issuecomment-868936485  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
