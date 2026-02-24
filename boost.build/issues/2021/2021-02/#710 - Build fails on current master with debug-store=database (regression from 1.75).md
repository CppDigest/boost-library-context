# #710 - Build fails on current master with debug-store=database (regression from 1.75) [Open]

> Username: thebrandre  
> Created at: 2021-02-13 12:06:55 UTC  
> Updated at: 2021-05-29 16:22:57 UTC  
> Url: https://github.com/boostorg/build/issues/710  

Operating System: Windows 1809  
Compiler: Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27045 for x64 (which is VS2017's compiler shipped with VS2019).  
  
I compile boost as decribed [here](https://github.com/boostorg/build/issues/269) via:  
```batch  
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\Build\vcvarsall.bat" x64 -vcvars_ver=14.16  
.\bootstrap.bat   
.\b2 toolset=msvc-14.1 -j8 architecture=x86 runtime-link=shared variant=debug,release --without-mpi --without-python --without-graph_parallel --stagedir=stage64 debug-symbols=on debug-store=database cflags=/FS pch=off --hash address-model=64 stage  
```  
This works perfectly fine with the current release boost-1.75 but is broken on the [current master](https://github.com/boostorg/boost/commit/f0b58842ae3de5212fd2770fad2573116a9eeb19) which is [this commit](https://github.com/boostorg/build/commit/76da80f33187a3d9e5336157cdfae12ce82e37eb) in boost.build.  
  
I get lots of error messages like this one:  
  
> fatal error C1041: cannot open program database 'D:\lib\boost_f0b58842\bin.v2\libs\wave\build\faa0db243f6e360880aded34dd7ff9cb\libboost_wave-vc141-mt-x64-1_76.pdb'; if multiple CL.EXE write to the same .PDB file, please use /FS  
  
It seems the cflags=/FS are no longer passed correctly to the compiler.  
  
Avoiding `debug-store=database` still works ... but comes without PDBs  
```batch  
.\b2 toolset=msvc-14.1 -j8 architecture=x86 runtime-link=shared variant=debug,release --without-mpi --without-python --without-graph_parallel --stagedir=stage64 debug-symbols=on pch=off --hash address-model=64 stage  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-02-13 12:58:56 UTC  
> Url: https://github.com/boostorg/build/issues/710#issuecomment-778615724  

This release fixed a long standing issue of incorrectly mixing `cflags` for c++ compiles vs just `cxxflags`. TO fix your compile pass in `cxxflags=/FS` instead.

---

## Comment 2

> Username: thebrandre  
> Created at: 2021-02-13 13:35:24 UTC  
> Url: https://github.com/boostorg/build/issues/710#issuecomment-778620466  

Replacing cflags by cxxflags didn't help, unfortunately.  
  
Using git bisect, I found out that the issue was introduced in [this commit](https://github.com/boostorg/build/commit/f5584205b0ee93904a5f2a9921459f99a1caa515).

---

## Comment 3

> Username: thebrandre  
> Created at: 2021-02-13 13:57:07 UTC  
> Url: https://github.com/boostorg/build/issues/710#issuecomment-778623282  

Update: on the current master, using both `cflags=/FS cxxflags=/FS` works; only one of them doesn't.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:27 UTC  
> Url: https://github.com/boostorg/build/issues/710#issuecomment-850859603  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
