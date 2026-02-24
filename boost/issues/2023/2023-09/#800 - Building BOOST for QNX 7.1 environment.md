# #800 - Building BOOST for QNX 7.1 environment [Closed]

> Username: Ashish-Gautam-22  
> Created at: 2023-09-06 12:32:12 UTC  
> Updated at: 2023-10-13 11:33:52 UTC  
> Closed at: 2023-10-13 11:33:40 UTC  
> Url: https://github.com/boostorg/boost/issues/800  

I am currently working on changing the BOOST library in our code from 1.68.0(on QNX 6.6) to 1.83.0(on QNX 7.1). QNX 7.1 is 64 bit so I am trying to build in 64bit mode now. We have upgraded the compiler version from 4.7.3 to 8.3.0 as well. The Jamfile.v2 is also modified to support the Platform flags.  
When I am trying to build BOOST libraries, I am getting errors.  
  
b2.exe gets created successfully. After setting the PATH, QNX_TARGET and QNX_HOST the following commands for Boost 1.68 were given in a .cmd file  
  
`set ADD_CXXFLAGS=-Wc,-std=gnu++11 -Wc,-fstack-protector-strong -Wc,-ftemplate-depth=1024 -Wc,-march=core2 -Wc,-mfpmath=sse -Wc,-fno-rounding-math -Wc,-msse2 -Wc,-fno-builtin -Wc,-w`  
`set ADD_PLATFORMFLAGS=target-os=qnx address-model=32 architecture=x86 abi=x32 binary-format=elf`  
`set AX_BOOST_FLAGS=define=BOOST_NO_CXX11_STD_ALIGN define=BOOST_NO_CXX11_HDR_CODECVT define="__GXX_EXPERIMENTAL_CXX0X__=1" define="_GLIBCXX_USE_C99=1" define=BOOST_NO_CXX11_HDR_TYPE_TRAITS define=BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS`  
  
`b2 --abbreviate-paths --hash link=static toolset=qcc variant=release %ADD_PLATFORMFLAGS% linkflags="-V4.7.3,gcc_ntox86_gpp" cxxflags="%ADD_CXXFLAGS% -V4.7.3,gcc_ntox86_gpp" linkflags="-lstdc++ -lsocket -lcxa" testing.launcher=rexec -q --without-python --without-mpi %AX_BOOST_FLAGS% cxxflags="-Wc,-O3"`  
  
This works fine.  
  
For QNX 7 BOOST 1.83.0 compilation I modified the QNX_HOST and QNX_TARGET values and then tried with the following commands:  
  
`set ADD_CXXFLAGS=-Wc,-std=gnu++11 -Wc,-fstack-protector-strong -Wc,-ftemplate-depth=1024 -Wc,-march=core2 -Wc,-mfpmath=sse -Wc,-fno-rounding-math -Wc,-msse2 -Wc,-fno-builtin -Wc,-w`  
`set ADD_PLATFORMFLAGS=target-os=qnx address-model=64 architecture=x86 abi=sysv binary-format=elf`  
`set AX_BOOST_FLAGS=define=BOOST_NO_CXX11_STD_ALIGN define=BOOST_NO_CXX11_HDR_CODECVT define="__GXX_EXPERIMENTAL_CXX0X__=1" define="_GLIBCXX_USE_C99=1" define=BOOST_NO_CXX11_HDR_TYPE_TRAITS define=BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS`  
  
`b2 --abbreviate-paths --hash link=static toolset=qcc variant=release %ADD_PLATFORMFLAGS% linkflags="-V8.3.0,gcc_ntox86_64_gpp" cxxflags="%ADD_CXXFLAGS% -V8.3.0,gcc_ntox86_64_gpp" linkflags="-lstdc++ -lsocket -lcxa" testing.launcher=rexec -q --without-python --without-mpi %AX_BOOST_FLAGS% cxxflags="-Wc,-O3"`  
  
This gives the following output:  
```  
qcc.compile.c++ bin.v2\libs\filesystem\build\188fab9c8237f69123e7ab007c54e97e\codecvt_error_category.o  
license check failed  
  
    "qcc" -Vgcc_ntox86_64 -Wc,-ftemplate-depth-128 -fvisibility-inlines-hidden -Wc,-O3 -Wc,-std=gnu++11 -Wc,-fstack-protector-strong -Wc,-ftemplate-depth=1024 -Wc,-march=core2 -Wc,-mfpmath=sse -Wc,-fno-rounding-math -Wc,-msse2 -Wc,-fno-builtin -Wc,-w -V8.3.0,gcc_ntox86_64_gpp -O3 -Wc,-finline-functions -Wc,-Wno-inline -fvisibility=hidden -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS -DBOOST_NO_CXX11_HDR_CODECVT -DBOOST_NO_CXX11_HDR_TYPE_TRAITS -DBOOST_NO_CXX11_STD_ALIGN -DNDEBUG -D_GLIBCXX_USE_C99=1 -D__GXX_EXPERIMENTAL_CXX0X__=1 -I"." -I"libs\filesystem\src" -c -o "bin.v2\libs\filesystem\build\188fab9c8237f69123e7ab007c54e97e\codecvt_error_category.o" "libs\filesystem\src\codecvt_error_category.cpp"  
```  
  
This output is repeated for a lot of files. Please feel free to ask for more details if this isn't sufficient.

---

## Comment 1

> Username: Alexey-ASM  
> Created at: 2023-09-19 22:56:08 UTC  
> Url: https://github.com/boostorg/boost/issues/800#issuecomment-1726648743  

do you use windows or linux environment to build?  
seems qcc doesn't see license file

---

## Comment 2

> Username: Ashish-Gautam-22  
> Created at: 2023-10-13 11:33:52 UTC  
> Url: https://github.com/boostorg/boost/issues/800#issuecomment-1761365546  

It was a license issue
