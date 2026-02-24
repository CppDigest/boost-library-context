# #395 - One test fails to compile after boostorg/gil submodule updated from 715d76 to 1d9fe2 [Closed]

> Username: spacelg  
> Created at: 2019-10-10 09:57:24 UTC  
> Updated at: 2020-03-10 18:54:04 UTC  
> Closed at: 2020-03-10 18:54:03 UTC  
> Url: https://github.com/boostorg/gil/issues/395  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule gil updated from 715d76 to 1d9fe2 [boost/gil@979d091](https://github.com/boostorg/boost/commit/979d0917125b6dcc47bc8871e1070699ec892c0e). Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\gil\test   
  
[log_x64_test_43.log](https://github.com/boostorg/gil/files/3711705/log_x64_test_43.log)  
  
ErrorMessage:  
.\boost/gil/extension/io/jpeg/tags.hpp(26): fatal error C1083: Cannot open include file: 'jerror.h': No such file or directory (d:\agent\_work\3\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1722)  
.\boost/gil/extension/io/png/tags.hpp(25): fatal error C1083: Cannot open include file: 'png.h': No such file or directory (d:\agent\_work\3\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1722)  
.\boost/gil/extension/io/raw/tags.hpp(13): fatal error C1083: Cannot open include file: 'libraw/libraw.h': No such file or directory (d:\agent\_work\3\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1722)  
.\boost/gil/extension/io/tiff/detail/log.hpp(14): fatal error C1083: Cannot open include file: 'tiffio.h': No such file or directory (d:\agent\_work\3\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1722)

---

## Comment 1

> Username: mloskot  
> Created at: 2019-10-10 10:29:58 UTC  
> Url: https://github.com/boostorg/gil/issues/395#issuecomment-540507037  

I believe the error message makes the cause of the compilation failure clear.  
  
https://github.com/boostorg/gil/blob/develop/README.md#requirements  
https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#install-dependencies  
  
> Headers and libraries of libjpeg, libpng, libtiff, libraw for the I/O extension

---

## Comment 2

> Username: spacelg  
> Created at: 2019-10-14 03:03:40 UTC  
> Url: https://github.com/boostorg/gil/issues/395#issuecomment-541489531  

Thanks for your reply.  
  
Our main job is to test the MSVC compiler using open source projects. The various libraries of boost in our test script are tested like this:  
  
...  
call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\function_types\test"              %_LOG_DIR%\log_x64_test_40.log   & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3043  %_TEST_ERROR_x64%  
call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\fusion\test"                      %_LOG_DIR%\log_x64_test_41.log   & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3044  %_TEST_ERROR_x64%  
call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\gil\test"                         %_LOG_DIR%\log_x64_test_43.log   & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3046  %_TEST_ERROR_x64%  
call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\graph\test"                       %_LOG_DIR%\log_x64_test_44.log   & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3047  %_TEST_ERROR_x64%  
call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\graph_parallel\test"              %_LOG_DIR%\log_x64_test_45.log   & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3048  %_TEST_ERROR_x64%  
...  
  
Is the extension in the gil library new? Can you provide us with the detailed steps below?  
  
Thanks,  
Lin

---

## Comment 3

> Username: mloskot  
> Created at: 2019-10-14 16:34:19 UTC  
> Updated at: 2019-10-14 16:35:28 UTC  
> Url: https://github.com/boostorg/gil/issues/395#issuecomment-541783235  

> Our main job is to test the MSVC compiler using open source projects.  
  
Whose job? (just being curious)  
  
> `call %_SCRIPT_DIR%\RunHelper.cmd ".\b2 -j4 variant=release --build-dir=..\out\x64rel libs\gil\test" %_LOG_DIR%\log_x64_test_43.log & if NOT "!ERRORLEVEL!" == "0" set _TEST_ERROR_x64=3046 %_TEST_ERROR_x64%`  
  
This will build all tests inside `libs/gil/test` folder:  
  
https://github.com/boostorg/gil/tree/master/test  
https://github.com/boostorg/gil/tree/develop/test  
  
that is, including tests that require the I/O extension and tests of the I/O extension itself, thus requiring third-party image format libraries like libjpeg, libpng, etc. See my https://github.com/boostorg/gil/issues/395#issuecomment-540507037  
  
> Is the extension in the gil library new?   
  
It is ~9 years old https://lists.boost.org/Archives/boost/2011/01/175935.php  
  
> Can you provide us with the detailed steps below?  
  
Steps to do what?   
  
Your `b2` invocation above seems fine. You need to be specific what you are missing.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-10 18:54:03 UTC  
> Url: https://github.com/boostorg/gil/issues/395#issuecomment-597255048  

Closing due to no feedback clarifying what is the actual problem. Feel free to reopen if the issue persists and you have anything to add.
