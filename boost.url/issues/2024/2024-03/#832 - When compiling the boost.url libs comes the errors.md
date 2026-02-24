# #832 - When compiling the boost.url libs comes the errors [Closed]

> Username: lofonder  
> Created at: 2024-03-13 14:05:59 UTC  
> Updated at: 2024-08-16 21:16:23 UTC  
> Closed at: 2024-08-16 16:52:26 UTC  
> Url: https://github.com/boostorg/url/issues/832  

`boost-install.generate-cmake-config-version- bin\boost\bin.v2\libs\wave\build\stage\boost_wave-config-version.cmake  
gcc.link.dll bin\boost\bin.v2\libs\url\build\gcc-7\debug\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw7-mt-d-x32-1_83.dll  
common.copy D:\proj\boost_1_83_0\stage\lib\cmake\boost_wave-1.83.0\boost_wave-config-version.cmake  
bin\boost\bin.v2\libs\wave\build\stage\boost_wave-config-version.cmake  
copy 1 file。  
boost-install.generate-cmake-variant- bin\boost\bin.v2\libs\url\build\gcc-7\debug\threadapi-win32\threading-multi\visibility-hidden\libboost_url-variant-mgw7-mt-d-x32-1_83-shared.cmake  
common.copy D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll.a  
**couldn't find the target file.**  
  
    copy /b "bin\boost\bin.v2\libs\url\build\gcc-7\debug\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw7-mt-d-x32-1_83.dll.a" + this-file-does-not-exist-A698EE7806899E69 "D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll.a"  
  
...failed common.copy D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll.a...  
gcc.compile.c++ bin\boost\bin.v2\libs\wave\build\gcc-7\debug\threadapi-win32\threading-multi\visibility-hidden\instantiate_cpp_grammar.o  
common.copy D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll  
**couldn't find the target file.**  
  
    copy /b "bin\boost\bin.v2\libs\url\build\gcc-7\debug\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw7-mt-d-x32-1_83.dll" + this-file-does-not-exist-A698EE7806899E69 "D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll"  
  
...failed common.copy D:\proj\boost_1_83_0\stage\lib\libboost_url-mgw7-mt-d-x32-1_83.dll...  
...on 800th target...  
common.copy D:\proj\boost_1_83_0\stage\lib\cmake\boost_url-1.83.0\libboost_url-variant-mgw7-mt-d-x32-1_83-shared.cmake`  
![image](https://github.com/boostorg/url/assets/6055083/d1e6bfce-8c8d-4f00-bf9f-0283dad12b62)  
  
  
I tested the 1.83 and 1.84 version of the boost.It always failed .Only failed on the url lib.But 1.81 version of the boost seemed worked well.  
Did I miss some variaties of the compiling?  
  
?is it related to the issue [issue](https://github.com/bfgroup/b2/commit/2474be56b42e191bbc0706d46177703b24a7e7c3)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-03-13 15:41:51 UTC  
> Url: https://github.com/boostorg/url/issues/832#issuecomment-1994709946  

Hi @lofonder, I believe this should be working in Boost 1.85.  
  
https://boostorg.jfrog.io/artifactory/main/beta/1.85.0.beta1/source/

---

## Comment 2

> Username: lofonder  
> Created at: 2024-03-15 14:53:26 UTC  
> Url: https://github.com/boostorg/url/issues/832#issuecomment-1999838466  

it is related to the gcc.jam file .Ive fixed by  adding the :T and I hope it would be useful for the next version u just mentioned.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-03-15 15:41:26 UTC  
> Url: https://github.com/boostorg/url/issues/832#issuecomment-1999935304  

I'm not sure. What exactly did you change?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-08-16 21:16:22 UTC  
> Url: https://github.com/boostorg/url/issues/832#issuecomment-2294325305  

The mingw fix: https://github.com/bfgroup/b2/commit/2474be56b42e191bbc0706d46177703b24a7e7c3
