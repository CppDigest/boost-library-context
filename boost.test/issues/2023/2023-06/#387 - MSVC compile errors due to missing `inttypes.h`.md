# #387 - MSVC compile errors due to missing `inttypes.h` [Closed]

> Username: Lastique  
> Created at: 2023-06-13 15:15:35 UTC  
> Updated at: 2023-06-16 07:37:50 UTC  
> Closed at: 2023-06-16 07:37:50 UTC  
> Url: https://github.com/boostorg/test/issues/387  

I believe, https://github.com/boostorg/test/pull/353 broke MSVC-11 and older, which now [fail](https://ci.appveyor.com/project/Lastique/log/build/job/2si63omk53bwo3ye#L590) with the following error:  
  
```  
.\boost/test/impl/execution_monitor.ipp(51) : fatal error C1083: Cannot open include file: 'inttypes.h': No such file or directory  
    call "bin.v2\standalone\msvc\msvc-11.0\address-model-32\msvc-setup.bat" x86 >nul  
 cl /Zm800 -nologo "libs\test\src\execution_monitor.cpp" -c -Fo"bin.v2\libs\test\build\msvc-11.0\release\address-model-32\threading-multi\execution_monitor.obj"     -TP /wd4675 /EHs /GR /O2 /Ob2 /W4 /MD /Zc:forScope /Zc:wchar_t -wd4275 -wd4671 -wd4673 -DBOOST_ALL_NO_LIB=1 -DBOOST_TEST_DYN_LINK=1 -DNDEBUG "-I."   
```  
  
CC @apolukhin.
