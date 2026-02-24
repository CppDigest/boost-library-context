# #490 - Boost can't be successfully configured to use ICU backend on Windows [Open]

> Username: datalogics-robb  
> Created at: 2021-03-13 21:55:17 UTC  
> Updated at: 2023-04-16 10:55:12 UTC  
> Url: https://github.com/boostorg/boost/issues/490  

When I attempt to use ICU as the internationalization backend for boost::locale and regex, the configure test always fails.  
  
Digging down it seems as though the test requires debug libraries to be present, as well as release libraries.  
  
ICU (on windows) builds it's dlls and lib files with a `d` suffix to denote "Debug", so it should be able to build and link against either a debug or a release build.  
  
I've reproduced this is 1.75.0 and releases back to 1.72.0  
  
Here is a bit of the relevant config.log from a boost 1.74.0 release build.  
```  
file C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu_obj.obj.rsp  
"libs\locale\src\..\build\has_icu_test.cpp" -Fo"C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu_obj.obj"    -TP /Z7 /Od /Ob0 /W3 /GR /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /Zc:throwingNew /favor:blend /wd4675 /EHs /std:c++14 -c   
-DBOOST_ALL_NO_LIB=1   
"-I."   
"-IC:\Users\robb\.conan\data\icu\68.2\_\_\package\611fcefb561f89074ece4ede322b83bfae850b30\include"  
compile-c-c++ C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu_obj.obj  
  
    call "C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\standalone\msvc\1516fc4ab8d17a0765a1f78ddebd56c2\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu_obj.obj.rsp"   
  
has_icu_test.cpp  
  
file C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.exe.rsp  
  
"C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu_obj.obj"      
"icudt.lib"   
"icuind.lib"   
"icuucd.lib"  
msvc.link C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.exe  
  
        call "C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\standalone\msvc\1516fc4ab8d17a0765a1f78ddebd56c2\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X64 /MANIFEST:EMBED /subsystem:console /out:"C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.exe" /LIBPATH:"C:\Users\robb\.conan\data\icu\68.2\_\_\package\611fcefb561f89074ece4ede322b83bfae850b30\lib"   @"C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.exe.rsp"  
      
LINK : fatal error LNK1181: cannot open input file 'icuind.lib'  
...failed msvc.link C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.exe C:\Users\robb\.conan\data\boost\1.74.0\_\_\build\491db9cc8564376c4db3d2f68ad28bbc2648a5a4\boost\bin.v2\libs\locale\build\aa5f60dfd728cc291a7e1357df68268d\has_icu.pdb...  
...failed updating 2 targets...  
...updated 4 targets...  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-03-14 04:24:19 UTC  
> Url: https://github.com/boostorg/boost/issues/490#issuecomment-798830655  

Is `icuind.lib` file present in the `C:\Users\robb\.conan\data\icu\68.2\_\_\package\611fcefb561f89074ece4ede322b83bfae850b30\lib` directory?

---

## Comment 2

> Username: datalogics-robb  
> Created at: 2021-03-15 12:48:07 UTC  
> Url: https://github.com/boostorg/boost/issues/490#issuecomment-799391802  

@grafikrobot No, the icu build will add the suffix "d" to the lib name only in a debug build.  
  
```  
 Directory of c:\Users\robb\.conan\data\icu\68.2\_\_\package\611fcefb561f89074ece4ede322b83bfae850b30\lib  
  
03/12/2021  02:27 PM    <DIR>          .  
03/12/2021  02:27 PM    <DIR>          ..  
03/12/2021  02:27 PM             1,686 icudt.lib  
03/12/2021  02:27 PM         2,776,692 icuin.lib  
03/12/2021  02:27 PM            12,584 icuio.lib  
03/12/2021  02:27 PM            37,330 icutest.lib  
03/12/2021  02:27 PM            70,118 icutu.lib  
03/12/2021  02:27 PM         1,029,402 icuuc.lib  
               6 File(s)      3,927,812 bytes  
               2 Dir(s)  248,625,819,648 bytes free  
```

---

## Comment 3

> Username: datalogics-robb  
> Created at: 2021-03-15 13:09:42 UTC  
> Url: https://github.com/boostorg/boost/issues/490#issuecomment-799406394  

for comparison, this is a debug build of ICU:  
  
```  
 Directory of c:\Users\robb\.conan\data\icu\68.2\_\_\package\dbdb9305d73267ff8a5b31aa172b706c2ff8e886\lib  
  
03/15/2021  07:50 AM    <DIR>          .  
03/15/2021  07:50 AM    <DIR>          ..  
03/15/2021  07:50 AM    <DIR>          icud  
03/15/2021  07:50 AM             1,700 icudtd.lib  
03/15/2021  07:50 AM         2,783,012 icuind.lib  
03/15/2021  07:50 AM            12,656 icuiod.lib  
03/15/2021  07:50 AM            37,466 icutestd.lib  
03/15/2021  07:50 AM            70,370 icutud.lib  
03/15/2021  07:50 AM         1,031,948 icuucd.lib  
               6 File(s)      3,937,152 bytes  
               3 Dir(s)  247,335,088,128 bytes free  
```

---

## Comment 4

> Username: emmenlau  
> Created at: 2023-04-16 10:55:12 UTC  
> Url: https://github.com/boostorg/boost/issues/490#issuecomment-1510291334  

Same issue with current ICU 73_1, released this week.
