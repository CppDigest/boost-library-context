# #45 Add vcpkg bin folder to PATH [Merged]

> Username: mloskot  
> Created at: 2018-03-15 16:22:39 UTC  
> Updated at: 2018-03-15 19:18:50 UTC  
> Merged at: 2018-03-15 18:56:36 UTC  
> Closed at: 2018-03-15 18:56:36 UTC  
> Url: https://github.com/boostorg/gil/pull/45  

Fix GIL IO tests issue with dependency DLLs not found.  
  
-----  
  
/cc @chhenning, @stefanseefeld   
  
```  
D:\dev\boost\boost\libs\gil (ml/add-vcpkg-bin-to-path) b2 address-model=32 include=%I% library-path=%L% io/test//simple  
..\..\libs\log\build\Jamfile.v2:45: Unescaped special character in argument <define>$(flag)=1  
D:/dev/boost/boost/libs/predef/check/../tools/check\predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - libjpeg                  : yes (cached)  
    - zlib                     : yes (cached)  
    - libpng                   : yes (cached)  
    - libtiff                  : yes (cached)  
...patience...  
...patience...  
...found 3397 targets...  
...updating 2 targets...  
testing.capture-output ..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "gil_io_tests/non_bit_aligned_image_test": class std::ios_base::failure: Unsupported descriptor for targa file: iostream stream error  
io\test\all_formats_test.cpp(30): last checkpoint: "non_bit_aligned_image_test" test entry  
  
*** 1 failure is detected in the test module "all_formats_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
    set Path=D:\dev\boost\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\threading-multi;D:\dev\boost\boost\bin.v2\libs\filesystem\build\msvc-14.1\debug\threading-multi;D:\dev\boost\boost\bin.v2\libs\system\build\msvc-14.1\debug\threading-multi;D:\dev\boost\boost\bin.v2\libs\test\build\msvc-14.1\debug\threading-multi;D:\dev\boost\boost\bin.v2\libs\timer\build\msvc-14.1\debug\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.exe"   > "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.output" 2>&1  
    set status=%ERRORLEVEL%  
    echo. >> "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.output"  
    echo EXIT STATUS: %status% >> "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.output"  
    if %status% EQU 0 (  
        copy "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.output" "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\..\bin.v2\libs\gil\io\test\all_formats_test.test\msvc-14.1\debug\threading-multi\all_formats_test.run...  
...failed updating 1 target...  
...skipped 1 target...  
```  
  
Next, merging #42 should fix the 'new' TARGA issue.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-03-15 16:25:40 UTC  
> Url: https://github.com/boostorg/gil/pull/45#issuecomment-373437252  

And that's it ? Now I'm embarrassed !! :-)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-15 16:34:39 UTC  
> Updated_at: 2018-03-15 16:35:19 UTC  
> Url: https://github.com/boostorg/gil/pull/45#issuecomment-373440396  

Yes, at least, that is what I'm seeing while reproducing `.appveyor.yml` environment/steps locally  
  
* libpng14.dll was not found  
  
![gil-vcpkg-dll-not-found-1](https://user-images.githubusercontent.com/80741/37476919-a984904a-2876-11e8-91e6-76ab51e5c459.png)  
  
* tiff.dll was not found  
  
![gil-vcpkg-dll-not-found-2](https://user-images.githubusercontent.com/80741/37476940-b8e15802-2876-11e8-9c5b-71142fc8c5b5.png)  
  
* Finally, obscure `EXIT STATUS: -1073741515` returned  
  
![gil-vcpkg-dll-not-found-3](https://user-images.githubusercontent.com/80741/37476981-d3424c60-2876-11e8-8377-442b70328d18.png)  
  
No reason to be embarrassed really. The `-1073741515` could equally mean something like `MSB6006: "CL.exe" exited with code -1073741515` :-)

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-15 16:43:10 UTC  
> Updated_at: 2018-03-15 16:43:41 UTC  
> Url: https://github.com/boostorg/gil/pull/45#issuecomment-373443293  

Looks like this fix indeed makes progress, https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.177-develop/job/ssmhfrylt1clkqel  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "gil_io_tests/non_bit_aligned_image_test": class std::ios_base::failure: Unsupported descriptor for targa file: iostream stream error  
libs\gil\io\test\all_formats_test.cpp(30): last checkpoint: "non_bit_aligned_image_test" test entry  
*** 1 failure is detected in the test module "all_formats_test"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
```

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-03-15 18:56:47 UTC  
> Url: https://github.com/boostorg/gil/pull/45#issuecomment-373486447  

OK, the new failure definitely looks like progress, and the fix is obvious enough. Thanks !

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-03-15 19:18:50 UTC  
> Url: https://github.com/boostorg/gil/pull/45#issuecomment-373492836  

I'm happy it helped.

---
