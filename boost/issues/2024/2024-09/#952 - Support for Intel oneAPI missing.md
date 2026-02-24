# #952 - Support for Intel oneAPI missing [Closed]

> Username: MarDiehl  
> Created at: 2024-09-16 11:42:40 UTC  
> Updated at: 2024-09-21 07:12:34 UTC  
> Closed at: 2024-09-21 07:12:34 UTC  
> Url: https://github.com/boostorg/boost/issues/952  

I'm reposting the issue from https://github.com/bfgroup/b2/issues/413 here because the lack of response.  
  
### Environment and version details  
  
* Operating System+version: Arch Linux  
* Compiler+version: Intel oneAPI 2024.2.1  
* Shell: zsh  
* B2 Version: B2 5.2.1 (OS=LINUX, jobs=8)  
* B2 Configuration: Output of `b2 --debug-configuration` in your project.  
not added because it uses gcc.  
  
### Brief problem description  
Options defined in https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam are incompatible with LLVM-based Intel compilers:  
- `-ip` is not supported anymore  
  - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L216  
- `-use-pch` should be `-include-pch` or `-pch-use`  
  - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L262  
  - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L273   
- `pch-create` should be `-Xclang -emit-pch -o` (https://www.intel.com/content/www/us/en/developer/articles/technical/building-boost-with-oneapi.html)  
  - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L293  
  - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L303  
 - the Fortran compiler is called `ifx`, not `ifort`  
   - https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/intel-linux.jam#L298  
  
### Steps to reproduce the issue  
Install Boost with  
```sh  
./bootstrap.sh --prefix=/tmp/boost  
./b2 install --without-python --without-graph_parallel --without-mpi cxxstd=17  toolset=intel-linux  link=shared address-model=64 architecture=x86 runtime-link=shared  
```  
  
### Actual behavior summary  
  
The compiler gives warnings and errors and installation of a few files fails.  
A few example errors are shown  
  
```  
intel-linux.compile.c++ bin.v2/libs/log/build/intel-linux-linux/release/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/trivial.o  
icpx: command line warning #10430: Unsupported command line options encountered  
These options as listed are not supported.  
For more information, use '-qnextgen-diag'.  
option list:   
	-ip  
	-wd177,780,2196,1782,193,304,981,1418,411,734,279  
```  
```  
...skipped <pbin.v2/libs/math/build/intel-linux-linux/release/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden>acoshl.o for lack of <pbin.v2/libs/math/build/intel-linux-linux/release/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden>pch.pchi...  
```  
  
```  
...failed updating 1 target...  
   intel-linux.compile.c++.pch bin.v2/libs/math/build/intel-linux-linux/release/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/pch.pchi  
```  
  
### Expected behavior summary  
  
b2 should support the most recent Intel C++ compiler (`icpx`)

---

## Comment 1

> Username: hainest  
> Created at: 2024-09-19 21:15:51 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2362212701  

> -ip is not supported anymore  
> -use-pch should be -include-pch or -pch-use  
> pch-create should be -Xclang -emit-pch -o  
  
These have been fixed in [spack](https://spack.io/) ([patch](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/boost/intel-oneapi-linux-jam.patch)). I've been meaning to push them upstream, but I guess now is a good time. I can submit a PR here, unless you're working on one.  
  
> the Fortran compiler is called ifx, not ifort  
  
Huh. I don't know why that hasn't been encountered in spack builds. As of 4e62a3e275 , the ony Fortran file I can find is `./numeric/odeint/performance/fortran_lorenz.f90`. Clearly, it's needed. I'll add that to my TODO for spack.

---

## Comment 2

> Username: MarDiehl  
> Created at: 2024-09-20 06:45:13 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2362950941  

> Huh. I don't know why that hasn't been encountered in spack builds. As of https://github.com/boostorg/boost/commit/4e62a3e2751916960747954484d2e0be5957b225 , the ony Fortran file I can find is ./numeric/odeint/performance/fortran_lorenz.f90. Clearly, it's needed. I'll add that to my TODO for spack.  
  
`ifort` exists in parallel and was the preferred compiler in the first oneAPI releases. But now, `ifx` is the default and `ifort`s removal has been scheduled.

---

## Comment 3

> Username: MarDiehl  
> Created at: 2024-09-20 06:46:09 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2362952242  

> I can submit a PR here, unless you're working on one.  
  
I can do that, but would mainly use code from your spack package. Let me know if that would be ok.

---

## Comment 4

> Username: hainest  
> Created at: 2024-09-20 17:57:52 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2364237274  

> > Huh. I don't know why that hasn't been encountered in spack builds. As of [4e62a3e](https://github.com/boostorg/boost/commit/4e62a3e2751916960747954484d2e0be5957b225) , the ony Fortran file I can find is ./numeric/odeint/performance/fortran_lorenz.f90. Clearly, it's needed. I'll add that to my TODO for spack.  
>   
> `ifort` exists in parallel and was the preferred compiler in the first oneAPI releases. But now, `ifx` is the default and `ifort`s removal has been scheduled.  
  
Ah, ok. That would explain it. Do you happen to know what version added `ifx`?

---

## Comment 5

> Username: hainest  
> Created at: 2024-09-20 17:58:34 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2364239836  

> > I can submit a PR here, unless you're working on one.  
>   
> I can do that, but would mainly use code from your spack package. Let me know if that would be ok.  
  
Of course! Let me know if you have any other questions about the patches there (there are lots).

---

## Comment 6

> Username: MarDiehl  
> Created at: 2024-09-21 05:06:26 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2365003751  

> Ah, ok. That would explain it. Do you happen to know what version added `ifx`?  
  
It was there since the release of oneAPI (https://fortran-lang.discourse.group/t/intel-releases-oneapi-toolkit-free-fortran-2018/471). It reached feature parity with the old compiler in the 2023.0.0 release: https://fortran-lang.discourse.group/t/intel-fortran-2023-launch/4929

---

## Comment 7

> Username: MarDiehl  
> Created at: 2024-09-21 07:12:34 UTC  
> Url: https://github.com/boostorg/boost/issues/952#issuecomment-2365038312  

Closing, because https://github.com/boostorg/build/issues/739 seems to be the right place for this issue
