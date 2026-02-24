# #197 - Clang-linux on windows 10/mingw will not build shared dll so can all jamfiles specify static? [Closed]

> Username: pabristow  
> Created at: 2020-02-27 10:25:31 UTC  
> Updated at: 2020-03-10 12:22:57 UTC  
> Closed at: 2020-03-10 12:22:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/197  

When using Clang on Windows 10 with mingw64  with b2 toolset=clang and user-config.jam using clang-linux  
  
(using clang-linux because clang-win doesn't seem to work in mysterious ways that I don't have the skills to try to fix).  
  
I get this error when I try to create any shared library.  
  
```  
 LINK : fatal error LNK1181: cannot open input file 'boost_unit_test_framework-clang9-mt-x64-1_71.dll'  
 clang++: error: linker command failed with exit code 1181 (use -v to see invocation)  
```  
  
This can be circumvented by building a static library instead.  
  
Specifically this library is preventing testing multiprecision examples.  
  
`  lib no_eh_eg_support : ../test/no_eh_test_support.cpp ;`  
  
fails to build the .dll thus  
  
```  
  lld-link: error: could not open 'no_eh_eg_support-clang9-mt-x64-1_73.dll': no such file or directory  
  clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
so I have added to the jamfile.v2 for /multiprecision/example, for example,  
  
` ``     <toolset>clang:<link>static  
```  
and this allow the build to run OK with Clang.  
  
but this applies to *all* Clang builds.  Is this acceptable?  
  
Or should I be testing for Windows-with-ming64 and restrict link static to this?  
  
And if so how?  
  
`<toolset>clang-mingw:<link>static`  
  
Does not work :-(

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-02-28 05:59:39 UTC  
> Updated at: 2020-02-28 06:00:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/197#issuecomment-592328241  

If this is the jamfile change inserted in #196  
then the CI build jobs did run successfully.

---

## Comment 2

> Username: pabristow  
> Created at: 2020-02-28 09:41:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/197#issuecomment-592432940  

As expected, after running locally with Clang 9.0.0, but I was concerned at the possible effect elsewhere on b2 Clang-Linux running on *nix rather than Windows. I also affects Boost.Math and other libraries). If @jzmaddock is concerned I'm sure he will comment.  (It is a pity that clang-win doesn't seem to work as expected).

---

## Comment 3

> Username: pabristow  
> Created at: 2020-03-10 12:22:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/197#issuecomment-597057488  

Closed as unsolved, but link static is a satisfactory workaround.
