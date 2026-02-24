# #191 - Missing boost_locale on a build for Haiku [Closed]

> Username: Begasus  
> Created at: 2023-08-26 08:04:48 UTC  
> Updated at: 2023-08-28 07:33:11 UTC  
> Closed at: 2023-08-28 07:33:11 UTC  
> Url: https://github.com/boostorg/locale/issues/191  

Hello, I'm looking into bumping our version to the latest 1.83.0 version, so far all looks fine but it seems I'm missing the library for boost_locale, am I missing something or is this something intentional?  
  
`EDIT` Some additional information  
OS: Haiku R1B4 64bit  
Compiler: gcc13.2.0

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-08-26 15:14:49 UTC  
> Url: https://github.com/boostorg/locale/issues/191#issuecomment-1694382510  

From the given information I can't tell. I'd need the full build output. I'd assume it found neither ICU nor Iconv which would skip the build with a message: https://github.com/boostorg/locale/blob/0552ffc29ff11e4fe130b7143ea6ac2bee7b15c6/build/Jamfile.v2#L257-L258  
  
Or some of the other [requirement](https://github.com/boostorg/locale/blob/0552ffc29ff11e4fe130b7143ea6ac2bee7b15c6/build/Jamfile.v2#L388-L402) isn't met

---

## Comment 2

> Username: Begasus  
> Created at: 2023-08-26 17:07:38 UTC  
> Updated at: 2023-08-26 17:14:35 UTC  
> Url: https://github.com/boostorg/locale/issues/191#issuecomment-1694412131  

Including the full build output, one thing I noticed in the start now is: `Unicode/ICU support for Boost.Regex?... not found.`  
I can't find the mentioned ECHO line in the build though.  
  
[build_log.txt](https://github.com/boostorg/locale/files/12446139/build_log.txt)  
  
EDIT This is with ICU 70.1.2 and Iconv 1.17.3

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-08-27 10:09:34 UTC  
> Updated at: 2023-08-27 10:20:23 UTC  
> Url: https://github.com/boostorg/locale/issues/191#issuecomment-1694626436  

Yes it looks like it found iconv and ICU:  
```  
    - iconv (separate)         : yes [3]  
    - icu                      : yes [3]  
```  
  
However it seems it didn't detect that the std library supports `<type_traits>` and even support for static_assert:  
  
```  
    - cxx11_static_assert      : no [2]  
    - cxx11_hdr_type_traits    : no [2]  
```  
  
Checking the Boost.Config sources this is influenced by `#if (BOOST_LIBSTDCXX_VERSION < 50100) || !defined(BOOST_LIBSTDCXX11)` which has `__cplusplus >= 201103` for the latter  
  
Both should be correctly set so I can't tell what in your setup causes the issue.  
  
~You can try invoking something like `./b2 -d+2 -a libs/config/checks//cxx11_hdr_type_traits` in that boost tree to to show the command executed for that check. Maybe with additional b2-args if you pass any in your setup.~     
Edit: Actually the commands invoked and potential error messages should be in `bin.v2/config.log` Search for `cxx11_hdr_type_traits` and `cxx11_static_assert`  
  
Note that the issue isn't specifically with Boost.Locale as indicated by many "C++03 support is deprecated..." messages

---

## Comment 4

> Username: Begasus  
> Created at: 2023-08-27 15:23:11 UTC  
> Updated at: 2023-08-27 17:12:29 UTC  
> Url: https://github.com/boostorg/locale/issues/191#issuecomment-1694695004  

The open PR to update our boost is linked above (it contains the recipe for the build and the patches I've added).  
Some of the patches have been around for a while and have been updated to the newer release.  
  
  
A major part I reworked (as that part changed in the source) to get the build started was here:  
```diff  
--- a/tools/build/src/engine/build.sh  
+++ b/tools/build/src/engine/build.sh  
@@ -325,6 +325,23 @@ case "${B2_TOOLSET}" in  
         CXX_VERSION_OPT=${CXX_VERSION_OPT:---version}  
         B2_CXXFLAGS_RELEASE="-O2 -s"  
         B2_CXXFLAGS_DEBUG="-O0 -g"  
+  
+        CXX=${CXX:=g++}  
+        # Check whether it's MinGW GCC, which has Windows headers and none of POSIX ones.  
+        machine=$(${CXX} -dumpmachine 2>/dev/null)  
+        if [ $? -ne 0 ]; then  
+            echo "B2_TOOLSET is gcc, but the 'gcc' command cannot be executed."  
+            echo "Make sure 'gcc' is in PATH, or use a different toolset."  
+            exit 1  
+        fi  
+        case $machine in  
+        *haiku*)  
+        B2_CXX="${CXX} -x c++ -std=c++17"  
+        B2_CXXFLAGS_RELEASE="-O2 -s -D_DEFAULT_SOURCE -lbsd"  
+        B2_CXXFLAGS_DEBUG="-O0 -g"  
+        ;;  
+        esac  
     ;;  
   
     intel-*)  
--   
```  
  
Seeing you mention gcc being a part of it (already did a new port for ICU73.2 to check also), it could very well be that I made a mistake here.  
boost1.70 still builds OK with gcc13, so somehow some detection is off here on our side.  
  
> Edit: Actually the commands invoked and potential error messages should be in `bin.v2/config.log` Search for `cxx11_hdr_type_traits` and `cxx11_static_assert`  
  
Looing in there I don't see anything special:  
  
```  
...found 1 target...  
...found 1 target...  
...updating 1 target...  
config-cache.write bin.v2/project-cache.jam  
...updated 1 target...  
```  
  
In that directory there is project-cache.jam wich contains:  
  
```  
  set "cxx11_static_assert-<deduced-address-model>64-<deduced-architecture>x86-<inlining>on-<threading>multi-<toolset-gcc:version>13-<toolset>gcc-<variant>release-<visibility>hidden" : "false" ;  
...  
  set "cxx11_hdr_type_traits-<deduced-address-model>64-<deduced-architecture>x86-<inlining>on-<threading>multi-<toolset-gcc:version>13-<toolset>gcc-<variant>release-<visibility>hidden" : "false" ;  
```  
  
EDIT: after running that command you supplied earlier I'm getting a better log, attatching it here  
  
[config.log.txt](https://github.com/boostorg/locale/files/12448307/config.log.txt)  
  
  
EDIT2: after hunting down found these functions are disabled in boost/config/platform/haiku.hpp? These havent changed over the version (looking into the files date it originates from 2014?)  
https://github.com/boostorg/config/blob/33b12777cff1c818e31ec33333cc821e9f506fd6/include/boost/config/platform/haiku.hpp#L19  
  
EDIT3: uncommenting both lines for these functions in haiku.hpp solves the problem and even gets me a new library boost_nowide.

---

## Comment 5

> Username: Flamefire  
> Created at: 2023-08-28 07:33:11 UTC  
> Url: https://github.com/boostorg/locale/issues/191#issuecomment-1695181870  

> EDIT2: after hunting down found these functions are disabled in boost/config/platform/haiku.hpp? These havent changed over the version (looking into the files date it originates from 2014?) https://github.com/boostorg/config/blob/33b12777cff1c818e31ec33333cc821e9f506fd6/include/boost/config/platform/haiku.hpp#L19  
>   
> EDIT3: uncommenting both lines for these functions in haiku.hpp solves the problem and even gets me a new library boost_nowide.  
  
Yes it seems those defect macros are wrong/outdated. Boost.Locale requires C++11 since Boost 1.81 (see the changelog) as well as some other libraries which is likely why you see this (only) now.  
  
I opened an issue for this in Boost.Config: https://github.com/boostorg/config/issues/482     
If you happen to know why those macros were defined (i.e. until when they were correct) please comment in that issue.  
  
Anyway, glad you were able to find and resolved that, thanks a lot!
