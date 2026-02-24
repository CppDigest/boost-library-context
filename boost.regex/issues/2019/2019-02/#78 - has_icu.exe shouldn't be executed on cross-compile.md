# #78 - has_icu.exe shouldn't be executed on cross-compile [Closed]

> Username: BrannonKing  
> Created at: 2019-02-25 22:19:24 UTC  
> Updated at: 2020-01-19 18:16:47 UTC  
> Closed at: 2020-01-19 18:16:47 UTC  
> Url: https://github.com/boostorg/regex/issues/78  

I get the "Exec format error" below when cross-compiling boost with ICU available. This is on Boost 69. It shouldn't happen because there is no way to execute has_icu.exe from Linux. The Locale build succeeds; it doesn't actually attempt to run the test EXE that it produces.  
```  
mkdir -p "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden"  
  
gcc.compile.c++ bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu_test.o  
  
    "x86_64-w64-mingw32-g++"   -fvisibility-inlines-hidden -std=c++11 -fvisibility=hidden -Wno-deprecated     -I/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/include     -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_HAS_ICU=1  -I"." -I"/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/include" -c -o "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu_test.o" "libs/regex/build/has_icu_test.cpp"  
  
gcc.link bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe  
  
    "x86_64-w64-mingw32-g++"  -Wl,-rpath -Wl,"/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/bin"  -o "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe" -Wl,--start-group "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu_test.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -g -fvisibility=hidden -fvisibility-inlines-hidden -L/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib     -L/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib -licudt -licuin -licuio -licutest -licutu -licuuc  
  
testing.unit-test bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.passed  
  
    LD_LIBRARY_PATH="/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/bin:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
     "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe"  && touch  "bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.passed"  
  
/bin/sh: 5: bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe: Exec format error  
...failed testing.unit-test bin.v2/libs/regex/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.passed...  
...failed updating 1 target...  
```

---

## Comment 1

> Username: chewi  
> Created at: 2019-04-22 15:57:39 UTC  
> Url: https://github.com/boostorg/regex/issues/78#issuecomment-485458324  

Also happens with Boost 1.70.0.

---

## Comment 2

> Username: arcashka  
> Created at: 2019-10-16 10:38:31 UTC  
> Url: https://github.com/boostorg/regex/issues/78#issuecomment-542639767  

Same with crossbuild from linux to Android  
boost 1.71.0 from conan-center
