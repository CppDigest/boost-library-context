# #529 - check-target-builds builds wrong variant. [Open]

> Username: jzmaddock  
> Created at: 2020-01-22 18:17:22 UTC  
> Updated at: 2021-05-29 17:22:24 UTC  
> Url: https://github.com/boostorg/build/issues/529  

I have an open regex ticket that appears to be a Boost.Build issue: in short when building release mode Boost, configuration tests are built in debug mode (so it appears).  
  
See https://github.com/boostorg/regex/issues/95.

---

## Comment 1

> Username: bebuch  
> Created at: 2020-02-03 18:52:05 UTC  
> Url: https://github.com/boostorg/build/issues/529#issuecomment-581561100  

For some targets the names `icuuc`, `icui18n` and `icudata` are always used. This is the case even if `ICU_ICUUC_NAME`, `ICU_ICUDT_NAME` and `ICU_ICUIN_NAME` are defined explicitly.  
  
This is my cross build from Linux to Windows via Mingw-w64:  
  
```  
./b2 toolset=gcc variant=release threading=multi link=static target-os=windows architecture=x86 address-model=64 abi=ms binary-format=pe include=/mingw/include library-path=/mingw/lib define=U_DISABLE_VERSION_SUFFIX=1 -sICU_ICUUC_NAME=sicuuc -sICU_ICUDT_NAME=sicudt -sICU_ICUIN_NAME=sicuin --layout=system --prefix=/install --without-python --without-graph_parallel --without-mpi -j $(nproc) install  
```  
  
My `/mingw/lib/` dir:  
  
```  
$ ls -l /mingw/lib/  
total 19084  
drwxr-xr-x 3 root root     4096 Feb  2 16:00 icu  
-rw-r--r-- 1 root root   371114 Feb  2 15:58 libbz2.a  
-rw-r--r-- 1 root root  1940512 Feb  2 16:08 liblzma.a  
-rwxr-xr-x 1 root root      876 Feb  2 16:08 liblzma.la  
-rwxr-xr-x 1 root root      736 Feb  2 16:00 libsicudt.a  
-rwxr-xr-x 1 root root 10895028 Feb  2 16:00 libsicuin.a  
-rwxr-xr-x 1 root root   143990 Feb  2 16:00 libsicuio.a  
-rwxr-xr-x 1 root root  5196448 Feb  2 16:00 libsicuuc.a  
-rw-r--r-- 1 root root   118758 Feb  2 16:01 libz.a  
-rw-r--r-- 1 root root   850738 Feb  2 16:07 libzstd.a  
drwxr-xr-x 1 root root     4096 Feb  2 16:00 pkgconfig  
```  
  
From `config.log`:  
  
```  
...updating 2 targets...  
gcc.compile.c++ bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu_obj.o  
libs/locale/src/../build/has_icu_test.cpp: In function 'int main()':  
libs/locale/src/../build/has_icu_test.cpp:26:12: warning: unused variable 'c' [-Wunused-variable]  
   26 |    UChar32 c = ::u_charFromName(U_UNICODE_CHAR_NAME, "GREEK SMALL LETTER ALPHA", &err);  
      |            ^  
gcc.link bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu.exe  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licudata  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licui18n  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licuuc  
collect2: error: ld returned 1 exit status  
  
    "x86_64-w64-mingw32-g++" -L"/mingw/lib"   -o "bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu.exe" -Wl,--start-group "bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu_obj.o"  -Wl,-Bstatic  -Wl,-Bdynamic -licudata -licui18n -licuuc -Wl,--end-group -m64 -mthreads -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed gcc.link bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu.exe...  
...failed updating 1 target...  
...updated 1 target...  
...found 2 targets...  
...updating 2 targets...  
gcc.compile.c++ bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu64_obj.o  
libs/locale/src/../build/has_icu_test.cpp: In function 'int main()':  
libs/locale/src/../build/has_icu_test.cpp:26:12: warning: unused variable 'c' [-Wunused-variable]  
   26 |    UChar32 c = ::u_charFromName(U_UNICODE_CHAR_NAME, "GREEK SMALL LETTER ALPHA", &err);  
      |            ^  
gcc.link bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu64.exe  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licudata  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licui18n  
/usr/local/lib/gcc/x86_64-w64-mingw32/9.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -licuuc  
collect2: error: ld returned 1 exit status  
  
    "x86_64-w64-mingw32-g++" -L"/mingw/lib"   -o "bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu64.exe" -Wl,--start-group "bin.v2/libs/locale/build/gcc-9.2.0/debug/target-os-windows/threading-multi/visibility-hidden/has_icu64_obj.o"  -Wl,-Bstatic  -Wl,-Bdynamic -licudata -licui18n -licuuc -Wl,--end-group -m64 -mthreads -g -fvisibility=hidden -fvisibility-inlines-hidden   
```  
  
After applying [this patch](https://github.com/boostorg/regex/commit/3b98dc901a589f7a50b336aa4573743e5f5a732c) which fixes another bug in the same area and setting filesystem links for the ICU static archives the compilation succeeds.

---

## Comment 2

> Username: mbluett88  
> Created at: 2020-08-19 01:39:59 UTC  
> Url: https://github.com/boostorg/build/issues/529#issuecomment-675802229  

This is what I am using under Linux Mint 20:  
  
./b2 threading=multi link=shared library-path=/usr/lib/x86_64-linux-gnu include=/usr/include cxxflags='-std=c++11' define=U_DISABLE_VERSION_SUFFIX=1 -sICU_ICUUC_NAME=sicuuc -sICU_ICUDT_NAME=sicudt -sICU_ICUIN_NAME=sicuin  
  
And it still doesn't recognize ICU.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:54 UTC  
> Url: https://github.com/boostorg/build/issues/529#issuecomment-850868230  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
