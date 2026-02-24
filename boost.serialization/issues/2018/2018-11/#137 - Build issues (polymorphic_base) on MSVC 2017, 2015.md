# #137 - Build issues (polymorphic_base) on MSVC 2017, 2015 [Closed]

> Username: jeking3  
> Created at: 2018-11-08 01:15:33 UTC  
> Updated at: 2019-02-24 18:51:05 UTC  
> Closed at: 2019-02-24 18:51:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/137  

Seeing this on develop:  
```  
commit efcd46eb1be621e212b3d31fae12cdd7230cdfe3 (HEAD -> develop, origin/develop, origin/HEAD)  
Author: Robert Ramey <ramey@rrsd.com>  
Date:   Sat Nov 3 06:23:22 2018 -0700  
  
    Just discovered that registering type via EXPORT doesn't work with polymorphic archives !!!  
    Corrected this.  
```  
```  
c:\boost\libs\serialization\test>..\..\..\b2.exe toolset=msvc-14.1 address-model=64 cxxflags=-permissive- cxxstd=latest  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_array : yes (cached)  
    - Boost.Config Feature Check: std_wstreambuf : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_forward_list : yes (cached)  
    - Boost.Config Feature Check: hash : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_map : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_set : yes (cached)  
    - Boost.Config Feature Check: slist : no  (cached)  
    - Boost.Config Feature Check: auto_ptr : no  (cached)  
...patience...  
...patience...  
...found 7148 targets...  
...updating 7 targets...  
msvc.link.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll  
   Creating library ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib and object ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.exp  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: virtual __cdecl polymorphic_base::~polymorphic_base(void)" (__imp_??1polymorphic_base@@UEAA@XZ) referenced in function "public: virtual __cdecl polymorphic_derived2::~polymorphic_derived2(void)" (??1polymorphic_derived2@@UEAA@XZ)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(void)" (__imp_??0polymorphic_base@@QEAA@XZ) referenced in function "public: __cdecl polymorphic_derived2::polymorphic_derived2(void)" (??0polymorphic_derived2@@QEAA@XZ)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(class polymorphic_base const &)" (__imp_??0polymorphic_base@@QEAA@AEBV0@@Z) referenced in function "public: __cdecl polymorphic_derived2::polymorphic_derived2(class polymorphic_derived2 const &)" (??0polymorphic_derived2@@QEAA@AEBV0@@Z)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class polymorphic_base & __cdecl polymorphic_base::operator=(class polymorphic_base const &)" (__imp_??4polymorphic_base@@QEAAAEAV0@AEBV0@@Z) referenced in function "public: class polymorphic_derived2 & __cdecl polymorphic_derived2::operator=(class polymorphic_derived2 const &)" (??4polymorphic_derived2@@QEAAAEAV0@AEBV0@@Z)  
..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll : fatal error LNK1120: 4 unresolved externals  
  
        call "..\..\..\bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DLL /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll" /IMPLIB:"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib"    @"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
  
...failed msvc.link.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb...  
...removing ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib  
...removing ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb  
...skipped <p..\..\..\bin.v2\libs\serialization\test\test_dll_exported.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>test_dll_exported.exe for lack of <p..\..\..\bin.v2\libs\serialization\test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib...  
...skipped <p..\..\..\bin.v2\libs\serialization\test\test_dll_exported.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>test_dll_exported.run for lack of <p..\..\..\bin.v2\libs\serialization\test\test_dll_exported.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>test_dll_exported.exe...  
...failed updating 3 targets...  
...skipped 3 targets...  
```  
Will try `HEAD~1` to see if that isolates it.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-08 13:06:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-436987506  

Also probably want to either clean up the test file or add it to .gitignore:  
```  
c:\boost\libs\serialization\test>git status  
On branch develop  
Your branch is up to date with 'origin/develop'.  
  
Untracked files:  
  (use "git add <file>..." to include in what will be committed)  
  
        testfile2  
  
nothing added to commit but untracked files present (use "git add" to track)  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-08 13:14:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-436989712  

When I go back one commit:  
```  
c:\boost\libs\serialization\test>git log  
commit 5778b99fd2f5aa5abce735a3a3cf00604e199603 (HEAD)  
Author: Robert Ramey <ramey@rrsd.com>  
Date:   Tue Oct 30 13:17:39 2018 -0700  
  
    fix typo in Jamfile.v2 which breaks Appveyor  
```  
There are still errors, but different:  
```  
c:\boost\libs\serialization\test>..\..\..\b2.exe toolset=msvc-14.1 address-model=64 cxxflags=-permissive- cxxstd=latest -q  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_array : yes (cached)  
    - Boost.Config Feature Check: std_wstreambuf : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_forward_list : yes (cached)  
    - Boost.Config Feature Check: hash : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_map : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_set : yes (cached)  
    - Boost.Config Feature Check: slist : no  (cached)  
    - Boost.Config Feature Check: auto_ptr : no  (cached)  
...patience...  
...patience...  
...found 7133 targets...  
...updating 33 targets...  
compile-c-c++ ..\..\..\bin.v2\libs\serialization\test\test_exported_text_archive.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\polymorphic_derived2.obj  
polymorphic_derived2.cpp  
polymorphic_base exported  
polymorphic_derived2 exported  
polymorphic_derived2.cpp(23): error C2375: 'polymorphic_derived2::serialize': redefinition; different linkage  
c:\boost\libs\serialization\test\polymorphic_derived2.hpp(48): note: see declaration of 'polymorphic_derived2::serialize'  
polymorphic_derived2.cpp(26): error C2447: '{': missing function header (old-style formal list?)  
  
    call "..\..\..\bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"..\..\..\bin.v2\libs\serialization\test\test_exported_text_archive.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\polymorphic_derived2.obj.rsp"  
  
...failed compile-c-c++ ..\..\..\bin.v2\libs\serialization\test\test_exported_text_archive.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi\polymorphic_derived2.obj...  
...skipped <p..\..\..\bin.v2\libs\serialization\test\test_exported_text_archive.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>test_exported_text_archive.exe for lack of <p..\..\..\bin.v2\libs\serialization\test\test_exported_text_archive.test\msvc-14.1\debug\address-model-64\cxxstd-latest-iso\threading-multi>polymorphic_derived2.obj...  
...failed updating 1 target...  
...skipped 1 target...  
```

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-08 20:00:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437135816  

Also happens on MSVC 2015: https://ci.appveyor.com/project/jeking3/serialization/builds/20150455/job/pkkcp1arnjddr0tw?fullLog=true#L3369  
  
Built from develop + extra CI:  
```  
commit efcd46eb1be621e212b3d31fae12cdd7230cdfe3 (HEAD -> develop, origin/develop, origin/HEAD)  
Author: Robert Ramey <ramey@rrsd.com>  
Date:   Sat Nov 3 06:23:22 2018 -0700  
  
    Just discovered that registering type via EXPORT doesn't work with polymorphic archives !!!  
    Corrected this.  
```

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-08 21:41:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437166101  

I think I have a handle on this.  Running it through CI now.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-09 12:21:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437344037  

@robertramey wrote (on another thread):  
  
> msvc.link.dll bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.dll  
Creating library bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.lib and object bin.v2\libs\serialization\test\msvc-14.1\release\address-model-64\threading-multi\dll_polymorphic_derived2-vc141-mt-x64-1_69.exp  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: virtual __cdecl polymorphic_base::~polymorphic_base(void)" (_imp??1polymorphic_base@@UEAA@XZ) referenced in function "public: virtual __cdecl polymorphic_derived2::~polymorphic_derived2(void)" (??1polymorphic_derived2@@UEAA@XZ)  
...  
>   
> This is not related to the "singleton" problem. In working to find the error in test_dll_exported on some platforms (which I thought was related to the "singleton" problem), I discovered an instance where mixing exported types serialized via base class pointers to polymorphic archives (LOL - now that is a mouthful) wouldn't link. This turns out to be trickier than I thought as I had to navigate the "visibility/import/export" labyrinth. I've got it fixed on platforms other than windows which I cannot test here. So this is still pending.  
>   
> As an aside - the test_dll_exported test failure on some very few platforms is also related to "visibility" This could be verified by building with visibility=global. This whole episode wasn't made any easier by the fact that global build with b2 changed the default visibility rules while I was doing this and for a while maintained different settings between develop and master. Very, very, very confusing and hard to deal with.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-09 12:22:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437344206  

On the polymorphic changes, I have something that passes more tests, but it may not be what was originally intended.  What I noticed is that the polymorphic_derived2 class was being declared as an export, but it was importing the polymorphic_base.  Is the intention to compile the test code and the polymorphic_base into the test EXE, and to compile the polymorphic_derived2 into the DLL?  If so, my changes aren't correct.  I modified it so that the base has the same linkage as the derived.  I think more work is needed here.  
  
Fork PR: https://github.com/jeking3/serialization/pull/4  
AppVeyor passes all MSVC tests now (but still has warnings like:  
```  
compile-c-c++ bin.v2\libs\serialization\test\test_exported_polymorphic_text_archive.test\msvc-14.1\release\address-model-64\threading-multi\test_exported.obj  
test_exported.cpp  
polymorphic_derived2 imported  
c:\projects\boost-root\libs\serialization\test\polymorphic_derived2.hpp(50): warning C4275: non dll-interface class 'polymorphic_base' used as base for dll-interface class 'polymorphic_derived2'  
c:\projects\boost-root\libs\serialization\test\polymorphic_base.hpp(41): note: see declaration of 'polymorphic_base'  
c:\projects\boost-root\libs\serialization\test\polymorphic_derived2.hpp(48): note: see declaration of 'polymorphic_derived2'  
compile-c-c++ bin.v2\libs\serialization\test\test_exported_polymorphic_text_archive.test\msvc-14.1\release\address-model-64\threading-multi\polymorphic_base.obj  
```  
):  
https://ci.appveyor.com/project/jeking3/serialization/builds/20164701  
  
Travis CI has UBSAN and Valgrind failures but everything else passes:  
https://travis-ci.org/jeking3/serialization/builds/452693625

---

## Comment 7

> Username: robertramey  
> Created at: 2018-11-09 16:26:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437413567  

as far as I know this issue has been addressed.  I checked it in yesterday.  Neither appveyor nor travis showed any problems with this.  I believe that you're working with an out of date copy somewhere.

---

## Comment 8

> Username: jeking3  
> Created at: 2018-11-09 16:30:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437414761  

Thanks, I will drop my changes and rebase the CI jobs at jeking3#4 and let's see what happens.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-11-09 17:22:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437431724  

@robertramey I rebased on origin/develop and removed any changes I had made to polymorphic tests.  `develop` is still failing:  
  
https://ci.appveyor.com/project/jeking3/serialization/builds/20180302/job/vfcj9od3dc8ddpl9?fullLog=true#L3372  
  
https://ci.appveyor.com/project/jeking3/serialization/builds/20180302

---

## Comment 10

> Username: robertramey  
> Created at: 2018-11-09 17:29:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437433951  

Hmmm - I'm looking at https://www.boost.org/development/tests/develop/developer/serialization.html   
Looks like they're passing there.  This is the develop branch on the main boost/serialization repo

---

## Comment 11

> Username: jeking3  
> Created at: 2018-11-09 18:10:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437445883  

https://www.boost.org/development/tests/develop/teeks99-09-p-win2012R2-64on64.html  
What does `--remove-test-targets` do?

---

## Comment 12

> Username: jeking3  
> Created at: 2018-11-09 18:18:25 UTC  
> Updated at: 2018-11-09 18:23:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437448214  

The same commit from the matrix fails locally in MSVC 2017:  
```  
c:\boost\libs\serialization\test>git log  
commit efcd46eb1be621e212b3d31fae12cdd7230cdfe3 (HEAD, personal/develop)  
Author: Robert Ramey <ramey@rrsd.com>  
Date:   Sat Nov 3 06:23:22 2018 -0700  
  
    Just discovered that registering type via EXPORT doesn't work with polymorphic archives !!!  
    Corrected this.  
  
( then I did a clean and then built with -a -j5, and then )  
  
c:\boost\libs\serialization\test>..\..\..\b2 address-model=64 --remove-test-targets cxxflags=/std:c++latest --abbreviate-paths -q  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_array : yes (cached)  
    - Boost.Config Feature Check: std_wstreambuf : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_forward_list : yes (cached)  
    - Boost.Config Feature Check: hash : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_map : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_unordered_set : yes (cached)  
    - Boost.Config Feature Check: slist : no  (cached)  
    - Boost.Config Feature Check: auto_ptr : no  (cached)  
...patience...  
...patience...  
...found 6822 targets...  
...using 2 temp targets...  
...updating 176 targets...  
...using <p..\..\..\bin.v2\libs\serialization\test\test_dll_exported.test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt>test_dll_exported.obj...  
msvc.link.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll  
   Creating library ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib and object ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.exp  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: virtual __cdecl polymorphic_base::~polymorphic_base(void)" (__imp_??1polymorphic_base@@UEAA@XZ) referenced in function "public: virtual __cdecl polymorphic_derived2::~polymorphic_derived2(void)" (??1polymorphic_derived2@@UEAA@XZ)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(void)" (__imp_??0polymorphic_base@@QEAA@XZ) referenced in function "public: __cdecl polymorphic_derived2::polymorphic_derived2(void)" (??0polymorphic_derived2@@QEAA@XZ)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __cdecl polymorphic_base::polymorphic_base(class polymorphic_base const &)" (__imp_??0polymorphic_base@@QEAA@AEBV0@@Z) referenced in function "public: __cdecl polymorphic_derived2::polymorphic_derived2(class polymorphic_derived2 const &)" (??0polymorphic_derived2@@QEAA@AEBV0@@Z)  
polymorphic_derived2.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: class polymorphic_base & __cdecl polymorphic_base::operator=(class polymorphic_base const &)" (__imp_??4polymorphic_base@@QEAAAEAV0@AEBV0@@Z) referenced in function "public: class polymorphic_derived2 & __cdecl polymorphic_derived2::operator=(class polymorphic_derived2 const &)" (??4polymorphic_derived2@@QEAAAEAV0@AEBV0@@Z)  
..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll : fatal error LNK1120: 4 unresolved externals  
  
        call "..\..\..\bin.v2\standalone\msvc\msvc-14.1\adrs-mdl-64\archt-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DLL /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll" /IMPLIB:"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib"    @"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
  
...failed msvc.link.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb...  
...removing ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.lib  
...removing ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.pdb  
...failed updating 1 target...  
```

---

## Comment 13

> Username: jeking3  
> Created at: 2018-11-09 18:27:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437451042  

By the way this error makes sense relative to the code... in `polymorphic_derived2.cpp`:  
```  
#define POLYMORPHIC_BASE_IMPORT  
#include "polymorphic_base.hpp"  
  
#define POLYMORPHIC_DERIVED2_EXPORT  
#include "polymorphic_derived2.hpp"  
```  
So that means when linking the DLL, it is looking to import `polymorphic_base` however I don't see anything making a DLL with the base in it.  
```  
c:\boost\libs\serialization\test>dir ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\  
 Volume in drive C is OS  
 Volume Serial Number is 2618-4C40  
  
 Directory of c:\boost\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt  
  
11/09/2018  01:17 PM    <DIR>          .  
11/09/2018  01:17 PM    <DIR>          ..  
11/09/2018  01:17 PM               381 dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll.manifest  
11/09/2018  01:17 PM               462 dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll.rsp  
11/09/2018  01:17 PM            25,040 dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.exp  
11/09/2018  01:17 PM         1,181,959 polymorphic_derived2.obj  
11/09/2018  01:17 PM               366 polymorphic_derived2.obj.rsp  
               5 File(s)      1,208,208 bytes  
               2 Dir(s)  632,926,552,064 bytes free  
```  
rsp file:  
```  
c:\boost\libs\serialization\test>type ..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dll_polymorphic_derived2-vc141-mt-gd-x64-1_69.dll.rsp  
  
"..\..\..\bin.v2\libs\serialization\test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\polymorphic_derived2.obj"  
"..\..\..\bin.v2\libs\serialization\build\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\boost_serialization-vc141-mt-gd-x64-1_69.lib"  
"..\..\..\bin.v2\libs\system\build\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\boost_system-vc141-mt-gd-x64-1_69.lib"  
"..\..\..\bin.v2\libs\filesystem\build\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\boost_filesystem-vc141-mt-gd-x64-1_69.lib"  
```  
Nothing is providing `polymorphic_base` in that link.

---

## Comment 14

> Username: jeking3  
> Created at: 2018-11-09 18:31:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437452304  

Okay, I am not going crazy.  That test IS failing in the matrix.  `test_dll_exported` is failing.

---

## Comment 15

> Username: robertramey  
> Created at: 2018-11-09 18:39:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437454646  

Right - test_dll_exported IS failing on the test matrix - that's the only failure for msvc platforms.    
  
dll polymorphic derived 2 is a dll exports derived2 but needs to import base.  This is setup by the macro attributes.

---

## Comment 16

> Username: jeking3  
> Created at: 2018-11-09 19:18:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-437465927  

In that environment nothing is providing the base in the link.  Are we missing a build directive to build `polymorphic_base` into its own DLL (exporting the base) that the `polymorphic_derived2` link then references as an import?

---

## Comment 17

> Username: res2k  
> Created at: 2018-11-13 17:05:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-438350984  

Also see https://github.com/boostorg/serialization/pull/139

---

## Comment 18

> Username: robertramey  
> Created at: 2019-02-24 18:50:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/137#issuecomment-466804471  

I think #139 fixed this.  I eventually concluded that b2 doesn't handle dlls which export symbols and import symbols from other dlls.
