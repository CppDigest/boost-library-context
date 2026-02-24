# #63 - Boost.Locale don't build on macOS (missing iconv) [Closed]

> Username: myakushka  
> Created at: 2021-06-18 13:57:14 UTC  
> Updated at: 2024-01-03 16:19:44 UTC  
> Closed at: 2022-09-13 08:49:11 UTC  
> Url: https://github.com/boostorg/locale/issues/63  

During build Boost.Locale, library iconv (which comes with Xcode) is not detected, so build Boost.Locale is skipped. This appeared in version 1.76 - in version 1.75 the build is successful.  
```  
Performing configuration checks  
- default address-model    : 64-bit [1]  
- default architecture     : x86 [1]  
- has stat::st_mtim        : no [2]  
- has stat::st_mtimensec   : no [2]  
- has stat::st_mtimespec   : yes [2]  
- has stat::st_birthtim    : no [2]  
- has stat::st_birthtimensec : no [2]  
- has stat::st_birthtimespec : yes [2]  
- has statx                : no [2]  
- has statx syscall        : no [2]  
- has_icu builds           : no [2]  
- iconv (libc)             : no [2]  
- iconv (separate)         : no [2] <--this  
- icu                      : no [2]  
- icu (lib64)              : no [2]  
- Boost.Locale needs either iconv or ICU library to be built.  
- iconv (libc)             : no [3]  
- iconv (separate)         : no [3] <--this  
- icu                      : no [3]  
- icu (lib64)              : no [3]  
- Boost.Locale needs either iconv or ICU library to be built.  
- lockfree boost::atomic_flag : no [2]  
- compiler supports SSE2   : yes [2]  
- compiler supports SSE4.1 : yes [2]  
```  
  
I also tried to install iconv separately with brew and pointed the path to it through `-sICONV_PATH.` But this did not give any results.  
  
  
iMac2019  
macOS 11.4 (20F71)  
Apple clang version 12.0.5 (clang-1205.0.22.9)  
Target: x86_64-apple-darwin20.5.0

---

## Comment 1

> Username: TheSalvator  
> Created at: 2022-09-06 15:56:30 UTC  
> Updated at: 2022-09-06 15:56:44 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1238344337  

Same with 1.80.0, 1.79.0 works fine

---

## Comment 2

> Username: TheSalvator  
> Created at: 2022-09-06 17:39:14 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1238463904  

Reverting the changes with executable file fixes the problem. It's strange)  
https://github.com/conan-io/conan-center-index/pull/12807/commits/afb0fd400b1f45ac3dd88e125d249e52e41403b6

---

## Comment 3

> Username: olekrisek  
> Created at: 2024-01-03 13:47:07 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1875396713  

It still doesn't build locale on 1.82; while 1.79 does. Is any of patches above still valid for 1.82?

---

## Comment 4

> Username: Flamefire  
> Created at: 2024-01-03 13:54:37 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1875406330  

> It still doesn't build locale on 1.82; while 1.79 does. Is any of patches above still valid for 1.82?  
  
No, the patch has been tested and included in 1.81 so your issue is likely different.

---

## Comment 5

> Username: olekrisek  
> Created at: 2024-01-03 14:24:18 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1875453728  

OK, I run on Intel Mac-mini, Samona, latest Xcode installed with all the tools. and build with:   
```  
rm -rf arm64 x86_64 universal stage bin.v2  
rm -f b2 project-config*  
export MACOSX_DEPLOYMENT_TARGET=12.3  
./bootstrap.sh cxxflags="-arch x86_64 -arch arm64" cflags="-arch x86_64 -arch arm64" linkflags="-arch x86_64 -arch arm64"  
./b2 toolset=clang-darwin target-os=darwin architecture=arm abi=aapcs cxxflags="-arch arm64" cflags="-arch arm64" linkflags="-arch arm64" -a  
mkdir -p arm64a && cp stage/lib/*.a arm64a  
mkdir -p arm64 && cp stage/lib/*.dylib arm64  
export MACOSX_DEPLOYMENT_TARGET=10.13  
./b2 toolset=clang-darwin target-os=darwin architecture=x86 cxxflags="-arch x86_64" cflags="-arch x86_64" linkflags="-arch x86_64" abi=sysv binary-format=mach-o -a  
mkdir x86_64 && cp stage/lib/*.dylib x86_64  
cp stage/lib/*.a x86_64  
mkdir universal  
for dylib in arm64/*; do   
  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal/$(basename $dylib);   
done  
  
mkdir universal_a  
for dylib in arm64a/*; do   
  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal_a/$(basename $dylib);   
done  
  
for dylib in universal/*; do  
  lipo $dylib -info;  
done  
  
for dylib in universal_a/*; do  
  lipo $dylib -info;  
done  
```  
This compiles all the libraries except locale:   
  
In the output it says:   
    - iconv (libc)             : no [2]  
    - iconv (separate)         : yes [2]  
    - icu                      : no [2]  
  
and....  
  
  - locale                   : building  
    
  but there is no boost_locale.a in the result folder.

---

## Comment 6

> Username: Flamefire  
> Created at: 2024-01-03 16:19:43 UTC  
> Url: https://github.com/boostorg/locale/issues/63#issuecomment-1875627878  

So it is a separate issue as iconv is clearly found. So please open a new issue and attach the full output of b2 and `config.log`
