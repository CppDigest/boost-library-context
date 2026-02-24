# #191 - clang-linux.link action contains a line that is too long (12657, max 8191): [Closed]

> Username: pabristow  
> Created at: 2020-02-03 11:47:13 UTC  
> Updated at: 2020-02-26 11:20:21 UTC  
> Closed at: 2020-02-26 11:20:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/191  

I have been trying improve the multiprecision docs and want to check that I haven't mucked up anything, so I tried to build all the tests.  
  
GCC and MSVC pass OK, but Clang with this command line  
  
I:\boost\libs\multiprecision\test>b2 -a --abbreviate-paths toolset=clang cxxstd=2a release address-model=64 > .mp_tests_clang_abbrev_3feb2020_1050.log  
  
fails   
  
  clang-linux.link action contains a line that is too long (12657, max 8191):  
  
I tried --abbreviate-paths and --hash but that doesn't help.  
  
So I went back to check the current develop branch    776fbfd..0cb5e09  develop    -> origin/develop Updating 776fbfd..0cb5e09   
  
but it fails similarly  
  
clang-linux.link action contains a line that is too long (12657, max 8191):  
  
Ideas ?   
  
Paul  
  
PS I note there are confusing number of warnings using the jamfile.v2  - should many of these be suppressed?  This makes it difficult to see new failures.  
  
"clang++"    -o "..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\sf_performance.exe" -Wl,--start-group "..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\sf_performance.obj" "..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\sf_performance_basic.obj" "..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\sf_performance_bessel.obj" "..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-  
  
<snip>  
  
"..\..\..\bin.v2\libs\multiprecision\performance\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\sf_performance_files\sf_performance_poly_18.obj" "..\..\..\bin.v2\libs\system\build\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrd-mlt\vsblt-hdn\libboost_system-clang9-mt-x64-1_73.lib" "..\..\..\bin.v2\libs\thread\build\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\libboost_thread-clang9-mt-x64-1_73.lib" "..\..\..\bin.v2\libs\chrono\build\clng-lnx-9.0.0\rls\cxstd-2a-iso\lnk-sttc\thrd-mlt\vsblt-hdn\libboost_chrono-clang9-mt-x64-1_73.lib"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -std=c++2a -fvisibility=hidden -fvisibility-inlines-hidden -pthread -Wno-unused-command-line-argument -w -fuse-ld=lld =lld

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-02-07 17:19:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/191#issuecomment-583509360  

This is a build system error: the clang toolset is really the clang-linux toolset and appends the full name of every single object file to the command line, so yes it does exceed the windows command line max length.  The clang-win toolset might do better, but I've never got that working properly :(  
  
BTW this really does work fine on Linux....

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-02-07 22:05:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/191#issuecomment-583636036  

> This is a build system error: the clang toolset is really the clang-linux toolset and appends the full name of every single object file to the command line, so yes it does exceed the windows command line max length.  
  
Struggled with this kind of thing a lot over the years.  
  
I do not know if clang front end supports this, but with GCC, it is possible to put the names of both long lists of include header paths as well as long lists of object files to be linked within separate ascii text documents. Thereby this reduces long command lines significantly. These filenames are then included with the "at" symbol, such as:  
  
```  
g++ -Wall -pedantic -O3 @include_paths.txt test.cpp -o test.out  
```

---

## Comment 3

> Username: pabristow  
> Created at: 2020-02-10 10:06:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/191#issuecomment-584046312  

OK thanks for both.  I think we can leave this as a 'feature' of the build system.  The Linux test is a sufficient for the Clang compiler Multiprecision test combination.  The only thing we might do is to skip the test if it is really on Windows (despite using b2's Clang-Linux).  The best thing would be to get b2's Clang-Win to work properly, but I haven't got this to work either, and cooking the jam(s) is not in my skill set.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-02-10 18:36:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/191#issuecomment-584285513  

>  I think we can leave this as a 'feature' of the build system.  
  
Yup.
