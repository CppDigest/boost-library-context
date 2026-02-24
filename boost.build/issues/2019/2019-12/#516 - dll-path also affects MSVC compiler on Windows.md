# #516 - dll-path also affects MSVC compiler on Windows [Open]

> Username: mloskot  
> Created at: 2019-12-17 22:30:46 UTC  
> Updated at: 2021-05-29 18:22:38 UTC  
> Url: https://github.com/boostorg/build/issues/516  

The current documentation of `dll-path` says:  
  
https://github.com/boostorg/build/blob/b54f53ccbca07548df52c3a3ea63af678858569d/src/tools/features/dll-feature.jam#L10-L13  
  
This is not correct according to what I can reproduce using `Boost.Build 4.1-git` from the current Boost develop branch and `toolset-msvc-14.2`  
  
I have `%HOME%\user-config.jam` with `dll-path` locations specified in the `project` requirements:  
  
```  
project  
  : requirements  
    <address-model>32:<dll-path>C:/vcpkg/installed/x86-windows/bin  
    <address-model>64:<dll-path>C:/vcpkg/installed/x64-windows/bin  
  ;  
  
using libpng  
  :  
  : <include>C:/vcpkg/installed/x86-windows/include <search>C:/vcpkg/installed/x86-windows/lib  
  : <address-model>32  
  ;  
  
using libpng  
  :  
  : <include>C:/vcpkg/installed/x64-windows/include <search>C:/vcpkg/installed/x64-windows/lib  
  : <address-model>64  
  ;  
  
...  
```  
  
Complete version of `user-config.jam` with specification of other libraries like libjpeg, libtiff, zlib is available from Boost.GIL repo [example/b2/user-config-windows-vcpkg.jam](https://github.com/boostorg/gil/blob/837888bef238e2473272c323b81f2d9d1bf6266c/example/b2/user-config-windows-vcpkg.jam).  
  
Next, building Boost.GIL tests that require libpng (and others):  
  
```console  
b2.exe -d2 toolset=msvc-14.2 address-model=32 libs/gil/test/extension/io//simple  
```  
  
confirms the `dll-path` locations are added to `Path` environment variable:  
  
```console  
testing.capture-output bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\asynch-exceptions-on\threading-multi\all_formats_test.run  
  
    set Path=C:\vcpkg\installed\x86-windows\bin;D:\boost.win\bin.v2\libs\filesystem\build\msvc-14.2\debug\asynch-exceptions-on\threading-multi;D:\boost.win\bin.v2\libs\system\build\msvc-14.2\debug\asynch-exceptions-on\threading-multi;D:\boost.win\bin.v2\libs\test\build\msvc-14.2\debug\asynch-exceptions-on\threading-multi;C:\vcpkg\installed\x86-windows\lib;%Path%  
...  
**passed** bin.v2\libs\gil\test\extension\io\all_formats_test.test\msvc-14.2\debug\asynch-exceptions-on\threading-multi\all_formats_test.test  
```  
  
Next, if I remove the whole `project` definition from the `user-config.jam`, then the `Path` is not updated and I'm getting the expected run-time errors due to the DLL being missing:  
  
![b2-without-dll-path-for-deps](https://user-images.githubusercontent.com/80741/71039618-d851cc00-2124-11ea-9086-ad5717e5c958.png)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:07 UTC  
> Url: https://github.com/boostorg/build/issues/516#issuecomment-850877338  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
