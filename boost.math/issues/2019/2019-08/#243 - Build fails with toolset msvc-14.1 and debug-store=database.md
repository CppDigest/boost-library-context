# #243 - Build fails with toolset msvc-14.1 and debug-store=database [Open]

> Username: thebrandre  
> Created at: 2019-08-13 15:18:09 UTC  
> Updated at: 2019-08-13 15:18:09 UTC  
> Url: https://github.com/boostorg/math/issues/243  

All the cpp files in math/src/tr1  fail to compile when building boost 1.7.0 with PDBs on Windows 7 with Visual Studio 2017:  
> call "C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Auxiliary\Build\vcvarsall.bat" x64  
> .\bootstrap.bat  
> .\b2 -j1 toolset=msvc-14.1 architecture=x86 address-model=64 runtime-link=shared variant=debug debug-store=database --without-mpi --without-python stage  
  
Note: you can also use a parallel build but then you need to add `cflags=/FS` to the command-line arguments of b2 to enable parallel writing of PDBs.   
  
>compile-c-c++-pch bin.v2\libs\math\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threading-multi\pch.pch pch.pch.cpp  
compile-c-c++ bin.v2\libs\math\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threading-multi\acosh.obj acosh.cpp  
libs\math\build\..\src\tr1\acosh.cpp(6): error C2859: bin.v2\libs\math\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threading-multi\libboost_math_c99-vc141-mt-gd-x64-1_70.pdb is not the pdb file that was used when this precompiled header was created, recreate the precompiled header.  
 call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\math\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threading-multi\acosh.obj.rsp"   
  
... followed by the same error message for each of the 126 cpp files in math/src/tr1.  
  
> ...failed updating 126 targets...  
...skipped 12 targets...  
...updated 313 targets...  
  
Some remarks:  
- I have the same issue on Windows 10 with both Visual Studio 2017 and 2019. So I guess, it is fairly easy to reproduce but I'd be happy to provide further information.  
- This is related to this [issue](https://github.com/boostorg/build/issues/269) submitted to boost.build.
