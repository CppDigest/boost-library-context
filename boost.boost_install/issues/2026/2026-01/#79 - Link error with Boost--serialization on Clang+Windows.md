# #79 - Link error with Boost::serialization on Clang+Windows [Open]

> Username: ahoarau  
> Created at: 2026-01-14 16:05:59 UTC  
> Updated at: 2026-01-14 18:18:17 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79  

Hi,  
  
I encountered some issues with `Boost::serialization` with `clang` on `windows`, and the workaround I found was to remove the `INTERFACE_COMPILE_DEFINITIONS`.  
  
The error:  
```  
lld-link: error: undefined symbol: __declspec(dllimport) public: void __cdecl boost::archive::archive_exception::`vbase dtor'(void)  
```  
  
The workaround:  
```cmake  
# Here we remove the INTERFACE_COMPILE_DEFINITIONS, it was containing "BOOST_SERIALIZATION_NO_LIB;BOOST_SERIALIZATION_DYN_LINK"  
set_property(TARGET Boost::serialization PROPERTY INTERFACE_COMPILE_DEFINITIONS)  
```  
  
As a side note, it is odd to me that the `INTERFACE_COMPILE_DEFINITIONS` is filled with `BOOST_SERIALIZATION_NO_LIB;BOOST_SERIALIZATION_DYN_LINK`, is that wanted ? I feel like it says "disable automatic linking, but also enable it" ?  
  
  
  
Some related discussions and commits:  
https://github.com/boostorg/boost_install/commit/258f7866c1cb1e4d1b3aebd0c4db594d373c686c  
https://github.com/boostorg/boost_install/issues/54  
https://github.com/boostorg/boost_install/issues/62

---

## Comment 1

> Username: pdimov  
> Created at: 2026-01-14 16:13:40 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750341120  

`BOOST_SERIALIZATION_NO_LIB` means "disable autolink".  
`BOOST_SERIALIZATION_DYN_LINK` means "this is a DLL build, use import/export as appropriate".  
As a general rule, if `BOOST_SERIALIZATION_DYN_LINK` is not set correctly, things tend not to work. I'm not sure what's going bad in your particular case.

---

## Comment 2

> Username: pdimov  
> Created at: 2026-01-14 16:18:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750361813  

When you say "Clang on Windows", which Clang do you mean? clang-cl.exe or MinGW? I see that you're using lld-link.

---

## Comment 3

> Username: ahoarau  
> Created at: 2026-01-14 16:24:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750389408  

@pdimov Thanks for the clarification 👍  
I'm using pixi (conda), installing `clangxx` and `lld` and `llvm-tools` (for llvm-ar).  
We now have a `clang++.exe` (i'm not using clang-cl)

---

## Comment 4

> Username: pdimov  
> Created at: 2026-01-14 16:38:40 UTC  
> Updated at: 2026-01-14 16:39:28 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750456847  

I'm not sure what `clang++.exe` this installs, presumably some MinGW one?  
  
From the error above I suspect that the problem is that Boost.Serialization hasn't been compiled with that same Clang, but with something else, because your Clang expects `__declspec(dllimport) public: void __cdecl boost::archive::archive_exception::'vbase dtor'(void)` to be an exported symbol, but it isn't.

---

## Comment 5

> Username: ahoarau  
> Created at: 2026-01-14 16:43:29 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750477847  

I believe it's taking the compilers from https://github.com/llvm/llvm-project/releases  
(same as `winget install llvm.llvm`).  
  
Yes you are right, the boost is built with `cl`.   
Strangement removing this compile def solved the link issue, and tests run fine.  
  
I'll try to setup a minimal example.

---

## Comment 6

> Username: pdimov  
> Created at: 2026-01-14 16:49:07 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750509035  

You should try to build Boost.Serialization with this Clang and see if that gets rid of the link error.  
  
Another thing to try (if possible) is to link with `link.exe` instead of lld-link.  
  
> Strangement removing this compile def solved the link issue, and tests run fine.  
  
This essentially removes all the `__declspec(dllimport)` annotations, which works for functions - at the expense of an additional indirection - but would break in the general case, e.g. for data members.

---

## Comment 7

> Username: ahoarau  
> Created at: 2026-01-14 17:35:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750761866  

OK I got a minimal reproducible env. I can reproduce the linking error (on a basic serialization example from the boost website).  
I can also confirm that the workaround is working.

---

## Comment 8

> Username: ahoarau  
> Created at: 2026-01-14 17:39:52 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750791181  

The complete output:  
```  
Run Build Command(s): C:/dev/boost-cm/.pixi/envs/default/Library/bin/ninja.exe -v  
[1/2] C:\dev\boost-cm\.pixi\envs\default\Library\bin\clang++.exe -DBOOST_SERIALIZATION_DYN_LINK -DBOOST_SERIALIZATION_NO_LIB -isystem C:/dev/boost-cm/.pixi/envs/default/Library/include -O3 -DNDEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrt -MD -MT CMakeFiles/bs.dir/bs.cc.obj -MF CMakeFiles\bs.dir\bs.cc.obj.d -o CMakeFiles/bs.dir/bs.cc.obj -c C:/dev/boost-cm/bs.cc  
[2/2] C:\WINDOWS\system32\cmd.exe /C "cd . && C:\dev\boost-cm\.pixi\envs\default\Library\bin\clang++.exe -nostartfiles -nostdlib -O3 -DNDEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrt -Xlinker /subsystem:console -fuse-ld=lld-link CMakeFiles/bs.dir/bs.cc.obj -o bs.exe -Xlinker /MANIFEST:EMBED -Xlinker /implib:bs.lib -Xlinker /pdb:bs.pdb -Xlinker /version:0.0   C:/dev/boost-cm/.pixi/envs/default/Library/lib/boost_serialization.lib  -lkernel32 -luser32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 -loldnames  && cd ."  
FAILED: [code=1] bs.exe  
C:\WINDOWS\system32\cmd.exe /C "cd . && C:\dev\boost-cm\.pixi\envs\default\Library\bin\clang++.exe -nostartfiles -nostdlib -O3 -DNDEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrt -Xlinker /subsystem:console -fuse-ld=lld-link CMakeFiles/bs.dir/bs.cc.obj -o bs.exe -Xlinker /MANIFEST:EMBED -Xlinker /implib:bs.lib -Xlinker /pdb:bs.pdb -Xlinker /version:0.0   C:/dev/boost-cm/.pixi/envs/default/Library/lib/boost_serialization.lib  -lkernel32 -luser32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 -loldnames  && cd ."  
lld-link: error: undefined symbol: __declspec(dllimport) public: void __cdecl boost::archive::archive_exception::`vbase dtor'(void)  
>>> referenced by CMakeFiles/bs.dir/bs.cc.obj:(int `public: static void __cdecl boost::archive::save_access::save_primitive<class boost::archive::text_oarchive, struct boost::archive::tracking_type>(class archive::save_access::text_oarchive &, struct archive::save_access::tracking_type const &)'::`1'::dtor$3)  
>>> referenced by CMakeFiles/bs.dir/bs.cc.obj:(int `public: static void __cdecl boost::archive::save_access::save_primitive<class boost::archive::text_oarchive, class boost::archive::class_id_reference_type>(class archive::save_access::text_oarchive &, class archive::save_access::class_id_reference_type const &)'::`1'::dtor$3)  
>>> referenced by CMakeFiles/bs.dir/bs.cc.obj:(int `public: static void __cdecl boost::archive::save_access::save_primitive<class boost::archive::text_oarchive, class boost::archive::class_id_type>(class archive::save_access::text_oarchive &, class archive::save_access::class_id_type const &)'::`1'::dtor$3)  
>>> referenced 13 more times  
  
lld-link: error: undefined symbol: public: void __cdecl boost::archive::archive_exception::`vbase dtor'(void)  
>>> referenced by CMakeFiles/bs.dir/bs.cc.obj:(_TI2?AVarchive_exception@archive@boost@@)  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
ninja: build stopped: subcommand failed.  
```

---

## Comment 9

> Username: ahoarau  
> Created at: 2026-01-14 17:52:13 UTC  
> Updated at: 2026-01-14 17:52:21 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750861626  

Difficult to use link with clang (or clang-cl).  
```cmake  
set_property(TARGET Boost::serialization PROPERTY INTERFACE_COMPILE_DEFINITIONS BOOST_SERIALIZATION_NO_LIB)  
```  
Removing the `BOOST_SERIALIZATION_DYN_LINK` works as well.

---

## Comment 10

> Username: ahoarau  
> Created at: 2026-01-14 18:02:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750906599  

Same error with `link.exe`, and `clang-cl`:  
```  
Run Build Command(s): C:/dev/boost-cm/.pixi/envs/default/Library/bin/ninja.exe -v  
[1/2] C:\dev\boost-cm\.pixi\envs\default\Library\bin\clang-cl.exe  /nologo -TP -DBOOST_SERIALIZATION_DYN_LINK -DBOOST_SERIALIZATION_NO_LIB -imsvcC:\dev\boost-cm\.pixi\envs\default\Library\include /DWIN32 /D_WINDOWS /EHsc /O2 /Ob2 /DNDEBUG -MD /showIncludes /FoCMakeFiles\bs.dir\bs.cc.obj /FdCMakeFiles\bs.dir\ -c -- C:\dev\boost-cm\bs.cc  
[2/2] C:\WINDOWS\system32\cmd.exe /C "cd . && C:\dev\boost-cm\.pixi\envs\default\Library\bin\cmake.exe -E vs_link_exe --msvc-ver=1944 --intdir=CMakeFiles\bs.dir --rc=C:\PROGRA~2\WI3CF2~1\10\bin\100261~1.0\x64\rc.exe --mt=C:\dev\boost-cm\.pixi\envs\default\Library\bin\llvm-mt.exe --manifests  -- C:\PROGRA~1\MICROS~1\2022\COMMUN~1\VC\Tools\MSVC\1444~1.352\bin\Hostx64\x64\link.exe /nologo CMakeFiles\bs.dir\bs.cc.obj  /out:bs.exe /implib:bs.lib /pdb:bs.pdb /version:0.0 /machine:x64 /INCREMENTAL:NO /subsystem:console  C:\dev\boost-cm\.pixi\envs\default\Library\lib\boost_serialization.lib  kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib && cd ."  
FAILED: [code=4294967295] bs.exe  
C:\WINDOWS\system32\cmd.exe /C "cd . && C:\dev\boost-cm\.pixi\envs\default\Library\bin\cmake.exe -E vs_link_exe --msvc-ver=1944 --intdir=CMakeFiles\bs.dir --rc=C:\PROGRA~2\WI3CF2~1\10\bin\100261~1.0\x64\rc.exe --mt=C:\dev\boost-cm\.pixi\envs\default\Library\bin\llvm-mt.exe --manifests  -- C:\PROGRA~1\MICROS~1\2022\COMMUN~1\VC\Tools\MSVC\1444~1.352\bin\Hostx64\x64\link.exe /nologo CMakeFiles\bs.dir\bs.cc.obj  /out:bs.exe /implib:bs.lib /pdb:bs.pdb /version:0.0 /machine:x64 /INCREMENTAL:NO /subsystem:console  C:\dev\boost-cm\.pixi\envs\default\Library\lib\boost_serialization.lib  kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib && cd ."  
LINK: command "C:\PROGRA~1\MICROS~1\2022\COMMUN~1\VC\Tools\MSVC\1444~1.352\bin\Hostx64\x64\link.exe /nologo CMakeFiles\bs.dir\bs.cc.obj /out:bs.exe /implib:bs.lib /pdb:bs.pdb /version:0.0 /machine:x64 /INCREMENTAL:NO /subsystem:console C:\dev\boost-cm\.pixi\envs\default\Library\lib\boost_serialization.lib kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib /MANIFEST:EMBED,ID=1" failed (exit code 1120) with the following output:  
   Creating library bs.lib and object bs.exp  
bs.cc.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: void __cdecl boost::archive::archive_exception::`vbase destructor'(void)" (__imp_??_Darchive_exception@archive@boost@@QEAAXXZ) referenced in function "int `public: static void __cdecl boost::archive::save_access::save_primitive<class boost::archive::text_oarchive,struct boost::archive::tracking_type>(class archive::save_access::text_oarchive &,struct archive::save_access::tracking_type const &)'::`1'::dtor$3" (?dtor$3@?0???$save_primitive@Vtext_oarchive@archive@boost@@Utracking_type@23@@save_access@archive@boost@@SAXAEAVtext_oarchive@12@AEBUtracking_type@12@@Z@4HA)  
bs.cc.obj : error LNK2001: unresolved external symbol "public: void __cdecl boost::archive::archive_exception::`vbase destructor'(void)" (??_Darchive_exception@archive@boost@@QEAAXXZ)  
bs.exe : fatal error LNK1120: 2 unresolved externals  
ninja: build stopped: subcommand failed.  
```

---

## Comment 11

> Username: ahoarau  
> Created at: 2026-01-14 18:18:10 UTC  
> Updated at: 2026-01-14 18:18:17 UTC  
> Url: https://github.com/boostorg/boost_install/issues/79#issuecomment-3750968431  

I compiles correctly if I only use the include directories:  
```cmake  
target_include_directories(bs PRIVATE ${Boost_INCLUDE_DIRS})  
# target_link_libraries(bs PRIVATE Boost::serialization)  
```  
  
The autolink feature seems to be enabled.
