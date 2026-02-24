# #323 - WIN64: 'undefined reference to `icudt62_dat`' error for builds using mingw-w64 with ICU [Open]

> Username: sav-ix  
> Created at: 2018-07-07 15:18:55 UTC  
> Updated at: 2021-06-11 01:55:56 UTC  
> Url: https://github.com/boostorg/build/issues/323  

Hello everyone,  
  
For builds using mingw-w64 got errors:  
```  
.\b2 --build-type=complete toolset=gcc-8.1.0 variant=release link=static threading=multi runtime-link=static address-model=64 -sICU_PATH="%CD%\..\..\libICU-63.1-dev\GCC64RT" -sICU_LINK="-L"%CD%\..\..\libICU-63.1-dev\GCC64RT\lib" -lsicuuc -lsicuin -lsicudt" -d2 -q  
Performing configuration checks  
  
<snip>  
  
    - icu                      : no  
    - icu (lib64)              : no  
  
<snip>  
  
    "g++"   -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1  -I"." -I"c:\libICU-63.1-dev\GCC64RT\include" -c -o "bin.v2\libs\locale\build\gcc-8.1.0\debug\has_icu64_obj.o" "libs\locale\src\..\build\has_icu_test.cpp"  
  
libs\locale\src\..\build\has_icu_test.cpp: In function 'int main()':  
libs\locale\src\..\build\has_icu_test.cpp:26:12: warning: unused variable 'c' [-Wunused-variable]  
    UChar32 c = ::u_charFromName(U_UNICODE_CHAR_NAME, "GREEK SMALL LETTER ALPHA", &err);  
            ^  
gcc.link bin.v2\libs\locale\build\gcc-8.1.0\debug\has_icu64.exe  
  
    "g++"  -Wl,-rpath -Wl,"c:\libICU-63.1-dev\GCC64RT\bin64"  -o "bin.v2\libs\locale\build\gcc-8.1.0\debug\has_icu64.exe" -Wl,--start-group "bin.v2\libs\locale\build\gcc-8.1.0\debug\has_icu64_obj.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -g -Lc:\libBOOST-1.68.0-dev\build\..\..\libICU-63.1-dev\GCC64RT\lib -lsicudt -lsicuin -lsicuuc  
  
c:\libBOOST-1.68.0-dev\build\..\..\libICU-63.1-dev\GCC64RT\lib/libsicuuc.a(udata.ao):udata.cpp:(.rdata$.refptr.icudt62_dat[.refptr.icudt62_dat]+0x0): undefined reference to `icudt62_dat'  
collect2.exe: error: ld returned 1 exit status  
...failed gcc.link bin.v2\libs\locale\build\gcc-8.1.0\debug\has_icu64.exe...  
...failed updating 1 target...  
...updated 1 target...  
...found 11 targets...  
...updating 8 targets...  
common.mkdir bin.v2\libs\log  
```  
during `libs\regex\build\has_icu_test.cpp` and `libs\locale\build\has_icu_test.cpp` build.  
  
Reproduced for:  
- builds using mingw-w64 with static ICU binaries,  
  
not reproduced for:  
- builds using mingw-w64 with shared ICU binaries,  
- builds using Windows ICC and MSVC with static ICU binaries.  
  
Environment:  
- Windows 10 x64 10.0.17134.1,  
- mingw-w64 x86_64 posix seh 8.1.0,  
- ICC 2018 Update 3,  
- MSVC 2017 15.7.3,  
- Windows SDK 10.0.17134.12,  
- Boost-1.68.0-dev-ddc1a4f91dd30a716384a5f85557101d7b45da85.  
  
A source of error is improper library order in the linker command:  
```  
-lsicudt -lsicuin -lsicuuc  
```  
while expected to be:  
```  
-lsicuuc -lsicudt -lsicuin  
```  
as in `libs\locale\build\Jamfile.v2:121:14` or  
```  
-lsicuin -lsicuuc -lsicudt  
```  
as used during ICU tools and test-suite build.  
ICU libraries order was set correctly in Boost configuration command (see above), but was changed by Boost build system (all `-l*` flags were set in alphabetical order). This have no effect for builds with shared ICU binaries but result to errors in case static binaries use.  
MSVC linker is also sensitive to the libraries order in build command, but for some reason has no such errors in this case.  
  
  
P.S. also drawing your attention, that `libs\regex\build\Jamfile.v2` and `libs\locale\build\Jamfile.v2` significantly differs, while relate to the same area (ICU support); presumably because 1st one wasn't covered with a commit like https://github.com/boostorg/locale/commit/1d7179612f4db1d27836577f690efe859e68a491 by @artyom-beilis.  
  
  
Best,  
  
Alexander

---

## Comment 1

> Username: BrannonKing  
> Created at: 2019-02-27 20:25:20 UTC  
> Url: https://github.com/boostorg/build/issues/323#issuecomment-468016713  

I too am struggling with this. I use this line:  
```  
-sICU_LINK="-lsicuio -lsicuin -lsicuuc -lsicudt"  
```  
Then, the test runs like this:  
```  
    "x86_64-w64-mingw32-g++"  -Wl,-rpath -Wl,"/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/bin"  -o "bin.v2/libs/locale/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe" -Wl,--start-group "bin.v2/libs/locale/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu_obj.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -g -fvisibility=hidden -fvisibility-inlines-hidden -L/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib     -lsicudt -lsicuin -lsicuio -lsicuuc  
  
/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib/libsicuuc.a(udata.ao):udata.cpp:(.rdata$.refptr.icudt62_dat[.refptr.icudt62_dat]+0x0): undefined reference to `icudt62_dat'  
```  
Who said the thing could re-order my libs? It appears that there must be code to remove duplicate libs, as I tried repeating a library but it got culled before the test. It's this code to remove duplicates that is probably losing the original ordering of the libs.

---

## Comment 2

> Username: BrannonKing  
> Created at: 2019-02-28 17:58:02 UTC  
> Url: https://github.com/boostorg/build/issues/323#issuecomment-468372824  

I had to work around this by using LDFLAGS instead of ICU_LINK.

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-02-28 18:26:13 UTC  
> Url: https://github.com/boostorg/build/issues/323#issuecomment-468382640  

AMDG  
  
On 2/27/19 1:25 PM, Brannon King wrote:  
> I too am struggling with this. I use this line:  
> ```  
> -sICU_LINK="-lsicuio -lsicuin -lsicuuc -lsicudt"  
> ```  
> Then, the test runs like this:  
> ```  
>     "x86_64-w64-mingw32-g++"  -Wl,-rpath -Wl,"/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/bin"  -o "bin.v2/libs/locale/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu.exe" -Wl,--start-group "bin.v2/libs/locale/build/gcc-7.3/debug/target-os-windows/visibility-hidden/has_icu_obj.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -g -fvisibility=hidden -fvisibility-inlines-hidden -L/home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib     -lsicudt -lsicuin -lsicuio -lsicuuc  
>   
> /home/brannon/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/lib/libsicuuc.a(udata.ao):udata.cpp:(.rdata$.refptr.icudt62_dat[.refptr.icudt62_dat]+0x0): undefined reference to `icudt62_dat'  
> ```  
> Who said the thing could re-order my libs? It appears that there must be code to remove duplicate libs, as I tried repeating a library but it got culled before the test. It's this code to remove duplicates that is probably losing the original ordering of the libs.  
>   
  
This is happening because of a combination of several factors:  
a) Jam splits -s arguments at spaces, thus ICU_LINK becomes  
   a list with each library as a separate element.  
b) Because ICU_LINK is a list, <linkflags>$(ICU_LINK) expands to  
   <linkflags>-L/home/... <linkflags>-lsucudt <linkflags>-lsiciun etc.  
   instead of <linkflags>"-L/home/... -lsicudt -lsicuin ...".  The  
   latter would preserve the order and is probably the correct  
   behavior.  
  
This could (and should) be avoided by the Boost.Locale  
Jamfile by using os.environ instead of modules.peek  
to access ICU_LINK.  
  
You can also work around it on the command line with  
extra quotes:  
"-sICU_LINK=\"...\""  
  
Additionally, if you don't use ICU_LINK, Boost.Build  
can order the libraries correctly on its own  ...or  
at least it would be able to if the Jamfile marked  
the dependencies correctly.  Another bug in the Boost.Locale  
Jamfile.  
  
Finally, this ticket should be assigned to Boost.Locale,  
not Boost.Build, as that's where it needs to be fixed.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:12 UTC  
> Url: https://github.com/boostorg/build/issues/323#issuecomment-859203431  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
