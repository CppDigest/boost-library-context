# #914 - No support for msvc-toolset 14.4x (VS 2022, 17.10.x) [Open]

> Username: pdeiml  
> Created at: 2024-05-28 09:53:07 UTC  
> Updated at: 2025-09-04 05:54:23 UTC  
> Url: https://github.com/boostorg/boost/issues/914  

This issue does not happen if one updates the Visual Studio version from e.g. 17.8 to 17.10. It happens if Visual Studio 17.10 is the only Visual Studio version which has been installed on the system. See end of this comment for further details.    
    
As the title says boost is not compilable with Visual Studio 17.10.x. This is most likely due to the minor version of the msvc-compiler and -toolst which jumps from version 14.3x (VS 17.8.x) up to 14.4x (VS 17.10.x). This is described by Microsoft in [msvc-toolst-minor-version-number-14-40](https://devblogs.microsoft.com/cppblog/msvc-toolset-minor-version-number-14-40-in-vs-2022-v17-10/).    
I think that a commit similar to [Add VC143, aka VS2012, aka cl.exe 17.x toolset.](https://github.com/boostorg/build/commit/aaf14325ec8e3ca86be9afaa501bca68fcfea8b3) might fix this.    
    
If one updates Visual Studio from e.g. 17.8 to 17.10 this problem will not occur because on the system there still will be the msvc-toolset 14.3x from the previous Visual Studio Version. If one completely deinstalls previous Visual Studio versions before the installation of Visual Studio 17.10 or one has an empty system and Visual Studio 17.10 is the first and only version which is installed then there will only be msvc-toolset version 14.4x and the compilation will fail.

---

## Comment 1

> Username: ekalchev  
> Created at: 2024-05-28 12:22:30 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2135085259  

Related issue on Microsoft  
https://developercommunity.visualstudio.com/t/Rollback-from-17100-to-1797-issue-wi/10666254?space=41&sort=newest&viewtype=all  
  
Workaround that fixes it for me  
  
Install MSVC v141 - VS 2017 C++ x64/x86 build tools (v14.16)

---

## Comment 2

> Username: sodevel  
> Created at: 2024-05-28 14:33:23 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2135388802  

AFAIK a standard installation of Visual Studio only keeps the latest MSVC runtime, so you will always run into this issue if you update to version 17.10 because this version ships with MSVC runtime 14.4x. The best workaround is to install a MSVC runtime 14.3 in parallel for your Visual Studio 17.10, `b2` should be able to pick it up (i haven't tested this though, if that fails, you can try and change the default runtime version to 14.3). The issue here is, that the first 3 numbers of the MSVC runtime version (14.4) are not identical to the toolset version which is still v143.  
  
Interestingly, `bootstrap.bat` is not affected by this issue and does work with MSVC runtime 14.4, it is only `b2` which does not. I tried to work around this by setting up the environment manually with `VsDevCmd.bat`, using a non-existing toolset version like `toolset=msvc-0.0`, but nothing works, `b2` never finds a matching compiler.

---

## Comment 3

> Username: pdeiml  
> Created at: 2024-05-29 06:08:27 UTC  
> Updated at: 2024-05-29 06:09:49 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2136586970  

@sodevel I am confused about the runtime- and toolset-version.    
I locally played around and changed files in analogy to [Add VC143, aka VS2012, aka cl.exe 17.x toolset.](https://github.com/boostorg/build/commit/aaf14325ec8e3ca86be9afaa501bca68fcfea8b3) and these locations correspond to the toolset version (there are comments and the names of the variables suggest that). With these changes I can build the parts of boost which I need.    
But if I understand you correctly the toolset version is still 14.3. But why does it then work if I change the locations which correspond to the toolset version? Or do these locations affect both, the toolset- and the runtime-version?    
Sorry if I understand something completely wrong.

---

## Comment 4

> Username: sodevel  
> Created at: 2024-05-30 20:10:34 UTC  
> Updated at: 2024-05-30 20:22:26 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2140789153  

All this versioning is quite confusing, to use a common naming, i am going to use the names from this website https://reactos.org/wiki/Visual_Studio_Versions.  
  
Before `VS version = 17.10.z` we had the situation that always `Toolset version = 143` and `MSVC version = 14.3x.yyyyy`. Since `VS version = 17.10.z` we have the situation `Toolset version = 143` and `MSVC version = 14.4x.yyyyy`. Now if you use `b2` with `toolset=msvc-14.4` it complains it cannot setup the compiler (i think, i can't remember fully and don't have access to the system right now, maybe it was `bootstrap.bat` that wasn't able to setup the compiler and `b2` always failed in the same way), if you use `toolset=msvc-14.3` it got further, but later the configure stage can't find a matching compiler.  
  
However, `b2` is smart, if you have still somewhere on your system a `MSVC version = 14.3x.yyyyy` available it will work even with `VS version = 17.10.z`. It will only error out if you only have `MSVC version = 14.4x.yyyyy`. I don't know what you did and how your system is setup, but are you sure that after your changes `b2` actually did use a `MSVC version = 14.4x.yyyyy` runtime?  
  
Edit: After re-reading your initial post i figured out i apparently described the same thing, just with more numbers. But i still don't understand what you changed, your link shows how support for a new `Toolset version` was added, but this is not necessary here, this version did not change.

---

## Comment 5

> Username: pdeiml  
> Created at: 2024-05-31 10:17:36 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2141708830  

Hey @sodevel ,  
thank you very much for your detailed answer that smooth out all misunderstandings about the versions and the difference between toolset- and runtime version.    
On my machine only ``VS 17.10.z`` is installed and building boost crashed. Then I did changes in analogy to the commit cited above in which the toolset version changed from 14.2x to 14.3x. Then it worked and I was happy :-)    
After reading your comment I am confused as well that it worked correctly because you are completely right. I changed the parts of boost which are about the toolset version which did not change in the minor version.    
    
I do not have such an insight knowledge about ``bootstrag`` and ``b2`` and boost as you have it. But is it correct that one needs to make a commit in boost to support the case if only ``VS 17.10.z`` is installed? Because if I understand you comment correctly in this case ``b2.exe`` will error out, won't it?

---

## Comment 6

> Username: sodevel  
> Created at: 2024-05-31 20:01:09 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2142903040  

I am also just a regular user and these are my observations, i only got some extra explanations for these from comments inside `tools/build/src/tools/msvc.jam`. On my system with only `MSVC version = 14.4x.yyyyy` available couldn't get `b2` running, so yes, i think something in `b2` needs to be changed.  
  
As an alternative you can build boost with CMake, the maintainers of the CMake build system and winapi library just made the required additions so that a CMake-build can act as a drop-in replacement for a b2-build (at least a rather default build of boost, maybe some configuration options are not available).

---

## Comment 7

> Username: ifilippov-pgi  
> Created at: 2024-06-10 22:56:36 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2159445304  

The `generate-setup-cmd()` helper in msvc.jam seems to be the culprit, at least in boost_1_82_0. The following patch fixed it for me:  
```diff  
--- boost_1_82_0/tools/build/src/tools/msvc.jam.orig    2023-04-10 09:48:06.000000000 -0400  
+++ boost_1_82_0/tools/build/src/tools/msvc.jam 2024-06-10 18:38:03.106212200 -0400  
@@ -1119,6 +1119,14 @@  
         }  
         else  
         {  
+            if [ MATCH "(14.4)" : $(version) ]  
+            {  
+                if $(.debug-configuration)  
+                {  
+                    ECHO "notice: [generate-setup-cmd] $(version) is 14.4x" ;  
+                }  
+                parent = [ path.native [ path.join  $(parent) "..\\..\\..\\..\\..\\Auxiliary\\Build" ] ] ;  
+            }  
             if [ MATCH "(14.3)" : $(version) ]  
             {  
                 if $(.debug-configuration)  
```

---

## Comment 8

> Username: kislinsk  
> Created at: 2024-06-11 09:37:50 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2160269992  

I can confirm that the fix from @ifilippov-pgi worked for us. I applied it to Boost v1.85 and built with Visual Studio 2022 v17.10.1.

---

## Comment 9

> Username: ifilippov-pgi  
> Created at: 2024-06-11 16:19:56 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2161154810  

There seems to be a caveat if you are using autolinking, though.   
  
If you specify a toolset when calling b2.exe, `toolset = msvc-17.0`, `msvc-14.4`, or `msvc-14.3` all resolve to Visual Studio 2022 during the build process as expected. However, the compiled binaries will result in different tags: `*-vc170-*.lib`, `*-vc144-*.lib`, and `*-vc143-*.lib`.  
  
vc170 and vc144 seem to break autolinking because linker will be looking for the vc143 tagged libraries even in VS 2022, 17.10. So you might want to make sure to use `toolset = msvc-14.3`.   
  
Probably some additional updates are needed to the boost headers now that Visual Studio and its toolset versions started to diverge.

---

## Comment 10

> Username: mwestphal  
> Created at: 2024-06-13 06:44:08 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2164651915  

the patch from @ifilippov-pgi  seems to works on our case too, with boost 1.83 build with MSVC 1940 (14.4).

---

## Comment 11

> Username: sodevel  
> Created at: 2024-06-18 13:23:06 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2176093957  

Looks like this was already fixed in the b2 build system, see https://github.com/bfgroup/b2/pull/394

---

## Comment 12

> Username: MarcinLisowski  
> Created at: 2024-06-19 09:26:21 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2178199994  

I can confirm it's fixed by `b2 5.2.1` - already checked that on my side. This version is already used on boost master branch, do we know when can we expect a release with it?

---

## Comment 13

> Username: zhang-zi-chuan  
> Created at: 2024-07-21 13:28:04 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2241611567  

> I can confirm it's fixed by `b2 5.2.1` - already checked that on my side. This version is already used on boost master branch, do we know when can we expect a release with it?  
  
how to employ `b2 5.2.1` in the building

---

## Comment 14

> Username: AraHaan  
> Created at: 2024-07-22 06:40:22 UTC  
> Updated at: 2024-07-22 06:41:38 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2242201582  

For some reason when I try the downloaded 1.85.0 zip release file b2.exe on me says no to everything and I have all the major versions of VS installed including:  
- VS2010 (10.x)  
- VS2012 (11.x)  
- VS2013 (12.x)  
- VS2015 (14.x)  
- VS2017 (15.x)  
- VS2019 (16.x)  
- VS2022 Preview (17.11+)

---

## Comment 15

> Username: m1mist  
> Created at: 2024-07-26 06:14:07 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2252047483  

> For some reason when I try the downloaded 1.85.0 zip release file b2.exe on me says no to everything and I have all the major versions of VS installed including:  
>   
> * VS2010 (10.x)  
> * VS2012 (11.x)  
> * VS2013 (12.x)  
> * VS2015 (14.x)  
> * VS2017 (15.x)  
> * VS2019 (16.x)  
> * VS2022 Preview (17.11+)  
  
I have the same problem

---

## Comment 16

> Username: sodevel  
> Created at: 2024-07-26 12:51:45 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2252700728  

Whatever your problem is, it is unrelated to this issue, this issue is about `b2` not working with a runtime version of 14.4+.

---

## Comment 17

> Username: fschlaef  
> Created at: 2024-09-03 10:30:49 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2326171025  

I am having the exact same issue on Windows 10 with Visual Studio 17.11.2

---

## Comment 18

> Username: sodevel  
> Created at: 2024-09-04 00:37:10 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2327689539  

Use Boost 1.86.0 and your issue is gone

---

## Comment 19

> Username: fschlaef  
> Created at: 2024-09-04 09:04:47 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-2328313968  

> Use Boost 1.86.0 and your issue is gone  
  
Couldn't do that because OpenUSD doesn't support it, but I noticed Boost 1.86.0 itself compiles with no issue 👍

---

## Comment 20

> Username: nigels-com  
> Created at: 2025-09-04 05:54:23 UTC  
> Url: https://github.com/boostorg/boost/issues/914#issuecomment-3252022806  

Is this issue resolved?  Can this be closed now?
