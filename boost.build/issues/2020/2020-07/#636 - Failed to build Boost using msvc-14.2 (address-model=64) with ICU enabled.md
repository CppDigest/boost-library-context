# #636 - Failed to build Boost using msvc-14.2 (address-model=64) with ICU enabled [Open]

> Username: laitingsheng  
> Created at: 2020-07-13 06:55:53 UTC  
> Updated at: 2021-05-29 17:22:27 UTC  
> Url: https://github.com/boostorg/build/issues/636  

I found that `boost` cannot be compiled correctly with ICU supports on Windows with Visual Studio 2019. Actually, I am using Conan to manage the packages, so ICU should be available. Here is the command issued by Conan:  
  
```text  
> C:\Users\laitingsheng\.conan\data\b2\4.2.0\_\_\package\456f15897172eef340fcbac8a70811f2beb26a93\bin\b2.exe target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=C:\.conan\9d68ed\1\source_subfolder\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=0 -sNO_ZSTD=0 -sICU_PATH=C:\Users\laitingsheng\.conan\data\icu\67.1\_\_\package\3f7cbf23e8a09e5a013f3be0b9f2ec8486fc6a71 boost.locale.iconv=off boost.locale.icu=on define=MINIZIP_DLL runtime-link=shared threading=multi link=shared variant=release --without-python toolset=msvc segmented-stacks=on define=BOOST_USE_SEGMENTED_STACKS=1 define=BOOST_USE_UCONTEXT=1 pch=on  install --prefix=C:\.conan\efe744\1 -j36 --abbreviate-paths --debug-configuration --build-dir="C:\.conan\1b56b9\1"  
```  
  
And obviously, `-sICU_PATH` was specified correctly. But `b2` gives the following output  
  
```text  
...  
  
    - has_icu builds           : no  
  
...  
  
    - icu                      : no  
    - icu (lib64)              : no  
  
...  
```  
  
When I read the `config.log` file under the build directory, I surprisingly found that it only tried to use `ICU_PATH/lib64` and `ICU_PATH/bin64` but never `ICU_PATH/lib` and `ICU_PATH/bin`:  
  
```text  
...  
  
compile-c-c++ C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu_obj.obj  
has_icu_test.cpp  
msvc.link C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu.exe  
LINK : fatal error LNK1181: cannot open input file 'icudt.lib'  
  
        call "C:\.conan\1b56b9\1\boost\bin.v2\standalone\msvc\msvc-14.2\adrs-mdl-64\archt-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu.exe" /LIBPATH:"C:\Users\laitingsheng\.conan\data\icu\67.1\_\_\package\3f7cbf23e8a09e5a013f3be0b9f2ec8486fc6a71\bin64" /LIBPATH:"C:\Users\laitingsheng\.conan\data\icu\67.1\_\_\package\3f7cbf23e8a09e5a013f3be0b9f2ec8486fc6a71\lib64"  @"C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu.exe.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
  
...failed msvc.link C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu.exe C:\.conan\1b56b9\1\boost\bin.v2\libs\locale\build\msvc-14.2\dbg\adrs-mdl-64\thrd-mlt\has_icu.pdb...  
...failed updating 2 targets...  
  
...  
```  
  
But in my understanding, `has_icu` should also query `ICU_PATH/lib` and `ICU_PATH/bin` to determine if `icu` exists. `ICU_PATH/lib64` and `ICU_PATH/bin64` is only for `icu (lib64)`.  
  
I think issue #620 is somewhat similar to this problem, and it has been posted for nearly 1 year without a reply.  
  
conan-io/conan-center-index#1704 proposed a fix to patch the `libs/locale/build/Jamfile.v2` file, but it appears to be an aggressive approach to resolve this issue.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:57 UTC  
> Url: https://github.com/boostorg/build/issues/636#issuecomment-850868246  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
