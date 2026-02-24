# #233 - improper x64 platform detection for builds using MSVC [Closed]

> Username: sav-ix  
> Created at: 2017-08-28 15:52:47 UTC  
> Updated at: 2018-02-06 20:26:41 UTC  
> Closed at: 2018-01-23 03:58:13 UTC  
> Url: https://github.com/boostorg/build/issues/233  

Hello, everyone,  
  
For Boost builds using MSVC with `address-model=64` configuration key, `b2` logs contain record:  
```  
    - 32-bit                   : yes  
```  
while for builds using mingw-w64 and ICC on Windows:  
```  
    - 32-bit                   : no  
    - 64-bit                   : yes  
```  
Wherein Boost installation folder contain binaries for platform x64, as expected.  
  
After some investigations found, that for builds using MSVC file `bin.v2/config.log` contain record:  
```  
file libs\config\checks\architecture\bin\msvc-14.0\debug\threading-multi\32.obj.rsp  
"libs\config\checks\architecture\32.cpp" -Fo"libs\config\checks\architecture\bin\msvc-14.0\debug\threading-multi\32.obj"    -TP /Od /Ob0 /W3 /GR /Zc:forScope /Zc:wchar_t /wd4675 -arch:AVX2 -fp:strict -Zc:wchar_t -FS -nologo -DWIN32 -D_WIN32 -DWIN64 -D_WIN64 -DUNICODE -D_UNICODE -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_DEPRECATE -GR -EHsc -O2 -DNDEBUG -D_NDEBUG -MD -c  
compile-c-c++ libs\config\checks\architecture\bin\msvc-14.0\debug\threading-multi\32.obj  
  
    call "C:\Users\test\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"libs\config\checks\architecture\bin\msvc-14.0\debug\threading-multi\32.obj.rsp"   
  
cl : Command line warning D9025 : overriding '/Od' with '/O2'  
32.cpp  
...updated 5 targets...  
...found 2 targets...  
...updating 1 target...  
```  
while for builds using mingw-w64 contain:  
```  
gcc.compile.c++ libs\config\checks\architecture\bin\gcc-mingw-7.2.0\debug\32.o  
  
    "g++"   -O0 -fno-inline -Wall -g -mtune=haswell -march=haswell -mavx2 -frounding-math -pipe -mthreads -DWIN32 -D_WIN32 -DWIN64 -D_WIN64 -DUNICODE -D_UNICODE -D__USE_MINGW_ANSI_STDIO -std=gnu++14 -frtti -fexceptions -O3 -DNDEBUG -D_NDEBUG    -c -o "libs\config\checks\architecture\bin\gcc-mingw-7.2.0\debug\32.o" "libs\config\checks\architecture\32.cpp"  
  
libs\config\checks\architecture\32.cpp:9:36: error: size of array 'test' is negative  
 int test[sizeof(void*) == 4? 1 : -1];  
                                    ^  
...failed gcc.compile.c++ libs\config\checks\architecture\bin\gcc-mingw-7.2.0\debug\32.o...  
...failed updating 1 target...  
...updated 3 targets...  
...found 2 targets...  
...updating 1 target...  
gcc.compile.c++ libs\config\checks\architecture\bin\gcc-mingw-7.2.0\debug\64.o  
  
    "g++"   -O0 -fno-inline -Wall -g -mtune=haswell -march=haswell -mavx2 -frounding-math -pipe -mthreads -DWIN32 -D_WIN32 -DWIN64 -D_WIN64 -DUNICODE -D_UNICODE -D__USE_MINGW_ANSI_STDIO -std=gnu++14 -frtti -fexceptions -O3 -DNDEBUG -D_NDEBUG    -c -o "libs\config\checks\architecture\bin\gcc-mingw-7.2.0\debug\64.o" "libs\config\checks\architecture\64.cpp"  
  
...updated 1 target...  
...found 2 targets...  
...updating 1 target...  
```  
and for builds using ICC on Windows:  
```  
file libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\32.obj.rsp  
"libs\config\checks\architecture\32.cpp" -Fo"libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\32.obj"    -TP /Od /Ob0 /W3 /GR /Zc:forScope /Zc:wchar_t /nologo /Qwn5 /Qwd985 -Qoption,c,--arg_dep_lookup /Qvc14 -Qpchi- /wd4675 -QxCORE-AVX2 -fp:strict -Qprec -Zc:wchar_t -nologo -DWIN32 -D_WIN32 -DWIN64 -D_WIN64 -DUNICODE -D_UNICODE -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_DEPRECATE -Qstd=c++14 -GR -EHsc -O3 -DNDEBUG -D_NDEBUG -MD -c  
compile-c-c++ libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\32.obj  
  
    call "C:\Users\test\AppData\Local\Temp\b2_intel-win_17.0_iclvars_ia32 vs2015.cmd" > nul   
    icl @"libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\32.obj.rsp"   
  
icl: command line warning #10120: overriding '/Od' with '/O3'  
32.cpp  
libs\config\checks\architecture\32.cpp(9): error: the size of an array must be greater than zero  
  int test[sizeof(void*) == 4? 1 : -1];  
           ^  
  
compilation aborted for libs\config\checks\architecture\32.cpp (code 2)  
...failed compile-c-c++ libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\32.obj...  
...failed updating 1 target...  
...updated 4 targets...  
...found 2 targets...  
...updating 1 target...  
  
file libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\64.obj.rsp  
"libs\config\checks\architecture\64.cpp" -Fo"libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\64.obj"    -TP /Od /Ob0 /W3 /GR /Zc:forScope /Zc:wchar_t /nologo /Qwn5 /Qwd985 -Qoption,c,--arg_dep_lookup /Qvc14 -Qpchi- /wd4675 -QxCORE-AVX2 -fp:strict -Qprec -Zc:wchar_t -nologo -DWIN32 -D_WIN32 -DWIN64 -D_WIN64 -DUNICODE -D_UNICODE -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_DEPRECATE -Qstd=c++14 -GR -EHsc -O3 -DNDEBUG -D_NDEBUG -MD -c  
compile-c-c++ libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\64.obj  
  
    call "C:\Users\test\AppData\Local\Temp\b2_intel-win_17.0_iclvars_ia32 vs2015.cmd" > nul   
    icl @"libs\config\checks\architecture\bin\intel-vc14-win-17.0\debug\threading-multi\64.obj.rsp"   
  
icl: command line warning #10120: overriding '/Od' with '/O3'  
64.cpp  
...updated 1 target...  
...found 2 targets...  
...updating 1 target...  
```  
Is it possible to bring the platform, detected during configuration, to match the target addres model and resulted binaries.  
  
  
Environment:  
- Windows 10 x64,  
- mingw-w64 6.3.0 x86_64  
- Intel Parallel Studio XE 2017 Update 4,  
- Visual Studio 2015 Update 3,  
- Boost-1.6x.0.  
  
  
Best,  
  
Alexander

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-14 20:09:51 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-351822250  

The behavior is correct.  The output is just misleading.

---

## Comment 2

> Username: sav-ix  
> Created at: 2017-12-18 12:54:47 UTC  
> Updated at: 2017-12-18 13:00:16 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-352417850  

Please consider, that according to builds logs in Description, builds using MSVC succeed 32-bit test, while builds using minw-w64 and ICC fail it, and succeed 64-bit test. Presumably because of:  
```  
b2_msvc_14.0_vcvarsall_x86.cmd  
```  
which is used during configuration, instead of:  
```  
b2_msvc_14.0_vcvarsx86_amd64.cmd  
```  
which is used during build.  
In addition to this issue, it results to the next one: even if 32-bit test succeed for 64-bit builds using MSVC, Boost binaries are build for 64-bit architecture. Which mean, that on some later configuration step 32-bit test results are explicitly replaced or shadowed by 64-bit, taken from build command or somewhere else (need be verified).

---

## Comment 3

> Username: swatanabe  
> Created at: 2017-12-18 15:46:18 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-352465845  

AMDG  
  
On 12/18/2017 05:54 AM, sav-ix wrote:  
> Please consider, that according to builds logs in Description, builds using MSVC succeed 32-bit test, while builds using minw-w64 and ICC fail it, and succeed 64-bit test. Presumably because of:  
> ```  
> b2_msvc_14.0_vcvarsall_x86.cmd  
> ```  
> is used for 32-bit test using MSVC, instead of:  
> ```  
> b2_msvc_14.0_vcvarsx86_amd64.cmd  
> ```  
> which is used during build.  
> In addition to this issue, it results to the next one: even if 32-bit test succeed for 64-bit builds using MSVC, Boost binaries are build for 64-bit architecture. Which mean, that on some later configuration step 32-bit test results are explicitly replaced or shadowed by 64-bit, taken from build command or somewhere else (need be verified).  
>   
  
  As I said, the output is misleading.  The test does not  
mean whether we are building for 32 or 64-bit.  It  
actually means the version that we would build  
if no architecture/address-model property is given.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-01-23 03:58:13 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-359669175  

I've updated boostcpp.jam to make the output clearer.

---

## Comment 5

> Username: sav-ix  
> Created at: 2018-02-06 19:45:15 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-363542217  

Updated Boost-1.67.0-dev sources to recent version. For configuration flags:  
```  
toolset=msvc-14.1 address-model=64  
```  
  
it still uses 32-bit compiler for tests:  
```  
file libs\config\checks\architecture\bin\msvc-14.1\debug\threading-multi\32.obj.rsp  
"libs\config\checks\architecture\32.cpp" -Fo"libs\config\checks\architecture\bin\msvc-14.1\debug\threading-multi\32.obj"    -TP /Od /Ob0 /W3 /GR /Zc:forScope /Zc:wchar_t /wd4675 -c  
compile-c-c++ libs\config\checks\architecture\bin\msvc-14.1\debug\threading-multi\32.obj  
  
    call "bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"libs\config\checks\architecture\bin\msvc-14.1\debug\threading-multi\32.obj.rsp"   
  
cl : Command line warning D9025 : overriding '/Od' with '/O2'  
32.cpp  
...updated 11 targets...  
...found 2 targets...  
...updating 1 target...  
```  
  
and prints:  
```  
- default address-model    : 32-bit  
```  
in stdout.  
  
> The test does not mean whether we are building for 32 or 64-bit. It actually means the version that we would build if no architecture/address-model property is given.  
  
Thank you for clarification. That explain the behavior, but it's practical use not obvious. The address model of output binaries could be useful for Boost Users to confirm expected build output. But for what purpose default address-model of compiler could be used?

---

## Comment 6

> Username: swatanabe  
> Created at: 2018-02-06 20:26:40 UTC  
> Url: https://github.com/boostorg/build/issues/233#issuecomment-363554280  

AMDG  
  
On 02/06/2018 12:45 PM, sav-ix wrote:  
> <snip>  
>> The test does not mean whether we are building for 32 or 64-bit. It actually means the version that we would build if no architecture/address-model property is given.  
>   
> Thank you for clarification. That explain the behavior, but it's practical use not obvious. The address model of output binaries could be useful for Boost Users to confirm expected build output. But for what purpose default address-model of compiler could be used?  
>   
  
  The reason that the test is done this way (and in  
fact, the only reason that the test is run at all  
if you pass address-model explicitly) is to decide  
which variant gets /address-model-nn/ in the target  
paths.  
  
In Christ,  
Steven Watanabe
