# #55 - -DBOOST_IOSTREAMS_NO_LIB not documented [Open]

> Username: spfuetzner  
> Created at: 2018-03-12 17:05:17 UTC  
> Updated at: 2018-06-17 17:03:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/55  

If iostreams is built with a prebuilt zlib library, BOOST_IOSTREAMS_NO_LIB needs to be defined to avoid linking errors (missing libboost_zlib...). This should be documented somewhere.

---

## Comment 1

> Username: eldiener  
> Created at: 2018-04-19 16:45:16 UTC  
> Updated at: 2018-04-19 16:45:33 UTC  
> Url: https://github.com/boostorg/iostreams/issues/55#issuecomment-382803812  

I am not seeing this. I can build iostreams with a pre-built zlib and there is no problem with the build and BOOST_IOSTREAMS_NO_LIB  is not defined on the command line. Maybe you need to specify what is your environment and what you are trying to "build".

---

## Comment 2

> Username: spfuetzner  
> Created at: 2018-05-04 14:35:34 UTC  
> Url: https://github.com/boostorg/iostreams/issues/55#issuecomment-386621169  

On Windows with Visual Studio 2015 I build boost with the following command:  
`b2 -j 10 --build-dir=build\x64 --stagedir=stage\x64 --toolset=msvc -sZLIB_BINARY=zlibstat -sZLIB_INCLUDE=L:\vc14\zlib\zlib-1.2.8 -sZLIB_LIBPATH=L:\vc14\zlib\zlib-1.2.8\contrib\vstudio\vc14\x64\ZlibStatReleaseWithoutAsm runtime-link=shared link=static threading=multi address-model=64 debug release stage`  
Linking against boost and my prebuild zlib results in linker errors. I had to define `BOOST_IOSTREAMS_NO_LIB` to make it work.

---

## Comment 3

> Username: eldiener  
> Created at: 2018-06-17 09:18:51 UTC  
> Url: https://github.com/boostorg/iostreams/issues/55#issuecomment-397865732  

According to the documentation the environment variable for specifying the library path is ZLIB_LIBRARY_PATH and not ZLIB_LIBPATH as you have it above.

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-06-17 17:03:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/55#issuecomment-397892170  

AMDG  
  
On 06/17/2018 03:18 AM, Edward Diener wrote:  
> According to the documentation the environment variable for specifying the library path is ZLIB_LIBRARY_PATH and not ZLIB_LIBPATH as you have it above.  
>   
>   
  
ZLIB_LIBPATH will actually work because I didn't want  
to break backwards compatibility.  
  
In Christ,  
Steven Watanabe
