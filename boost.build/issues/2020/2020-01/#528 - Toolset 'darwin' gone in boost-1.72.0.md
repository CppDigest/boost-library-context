# #528 - Toolset 'darwin' gone in boost-1.72.0? [Open]

> Username: peremato  
> Created at: 2020-01-17 09:51:17 UTC  
> Updated at: 2021-05-29 18:22:32 UTC  
> Url: https://github.com/boostorg/build/issues/528  

I cannot install version 1.72.0 on MacOS (10.15.2) with the recipes that worked for previous versions.  
`/bootstrap.sh --with-python=.../bin/python3.6 --with-toolset=darwin`  
  
I get the following error in bootstrap.log  
```  
Unknown toolset: darwin  
  
You can specify the toolset as the argument, i.e.:  
    ./build.sh gcc  
  
Toolsets supported by this script are:  
    acc, clang, como, gcc, intel-darwin, intel-linux, kcc, kylix, mipspro,  
    pathscale, pgi, qcc, sun, sunpro, tru64cxx, vacpp  
  
For any toolset you can override the path to the compiler with the CXX  
environment variable. You can also use additional flags for the compiler  
with the CXXFLAGS environment variable.  
  
A special toolset; cxx, is available which is used as a fallback when a more  
specific toolset is not found and the cxx command is detected. The 'cxx'  
toolset will use the CXX, CXXFLAGS, and LIBS environment variables, if present.  
  
Similarly, the cross-cxx toolset is available for cross-compiling by using the  
BUILD_CXX, BUILD_CXXFLAGS, and BUILD_LDFLAGS environment variables to compile  
binaries that will be executed on the build system. This allows CXX etc. to be  
set for cross-compilers to be propagated to subprocesses.  
```  
Any suggestion?

---

## Comment 1

> Username: hainest  
> Created at: 2020-01-30 21:38:39 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-580472275  

This was removed by https://github.com/boostorg/build/commit/eb8da52df3a43ca9616a7a6e11e0ad6fe896c031. I have contacted the author, and am awaiting a response. I will post back here once I have an update.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-01-31 01:31:41 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-580541076  

@hainest it was removed for building b2 as here is no difference between it and the clang toolset. Hence there was no point in maintaining duplicate code for both.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-01-31 01:32:24 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-580541257  

You should be able to make things work equally well by using `--with-toolset=clang`.

---

## Comment 4

> Username: peremato  
> Created at: 2020-01-31 07:32:53 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-580619033  

Thanks I'll try. Is this change in the toolchain also working for previous versions of Boost?

---

## Comment 5

> Username: hainest  
> Created at: 2020-01-31 20:01:24 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-580888957  

@grafikrobot That makes sense. It seems this would be a documentation bug, then.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:02 UTC  
> Url: https://github.com/boostorg/build/issues/528#issuecomment-850877315  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
