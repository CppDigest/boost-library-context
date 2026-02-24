# #704 - clang-libc++ CI build error [Closed]

> Username: sdarwin  
> Created at: 2021-01-20 23:16:35 UTC  
> Updated at: 2021-01-23 12:56:57 UTC  
> Closed at: 2021-01-22 15:52:55 UTC  
> Url: https://github.com/boostorg/build/issues/704  

A few boost libraries already have a job configured where the compiler was set to "clang-libc++" and the installed package "clang-10". For example,  https://github.com/boostorg/detail . The "clang-libc++" job had been succeeding before, but now says "Building B2 engine.. Could not find a suitable toolset."   Might be related to the new compiler detection logic.   Was the job incorrectly defined?    
  
Other repositories get the same result.  boostorg/utility, boostorg/iterator,   
  
I have reduced the size of a travis file from 20 jobs to 3 jobs to complete faster, https://github.com/sdarwin/detail  
  
Travis output:  https://travis-ci.org/github/sdarwin/detail/jobs/755425543  
  
Job definition:  
  
```  
    - os: linux  
      dist: xenial  
      compiler: clang-libc++  
      env: TOOLSET=clang COMPILER=clang++-10 CXXSTD=03,11,14,17,20 CXXFLAGS="-stdlib=libc++" LINKFLAGS="-stdlib=libc++"  
      addons:  
        apt:  
          packages:  
            - clang-10  
            - libc++-10-dev  
            - libc++abi-10-dev  
          sources:  
            - sourceline: "ppa:ubuntu-toolchain-r/test"  
            - sourceline: "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-10 main"  
              key_url: "https://apt.llvm.org/llvm-snapshot.gpg.key"  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-21 01:22:12 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764155469  

That looks like a misconfigured travis. But can't really tell since there's not enough info in those logs.

---

## Comment 2

> Username: mloskot  
> Created at: 2021-01-21 08:21:11 UTC  
> Updated at: 2021-01-21 08:22:11 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764465259  

I've started observing very similar issue on Circle CI builds for Boost.GIL   
  
- https://app.circleci.com/pipelines/github/boostorg/gil/140/workflows/a95189df-3737-4513-954b-abd458af1da9/jobs/16158  
- https://app.circleci.com/pipelines/github/boostorg/gil/141/workflows/bd418ed8-2d32-450a-b410-f4045b821946/jobs/16160  
  
As you can see, there is a custom "Checking compiler version" step and it shows correct and expected output  
  
```  
#!/bin/bash -eo pipefail  
echo TOOLSET=$TOOLSET  
echo VARIANT=$VARIANT  
echo OPTIMIZ=$OPTIMIZ  
echo CXXSTD=$CXXSTD  
echo LNKFLAG=$LNKFLAG  
$TOOLSET --version  
  
TOOLSET=clang  
VARIANT=variant=release  
OPTIMIZ=optimization=speed  
CXXSTD=11  
LNKFLAG=  
clang version 3.9.1-19 (tags/RELEASE_391/rc2)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
CircleCI received exit code 0  
```  
  
Then, the build step is failing  
  
```  
#!/bin/bash -eo pipefail  
cd boost-root && ./bootstrap.sh -with-toolset=$TOOLSET || cat ./bootstrap.log  
  
Building B2 engine..  
  
Could not find a suitable toolset.  
  
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
  
  
Failed to build B2 build engine  
cat: ./bootstrap.log: No such file or directory  
  
Exited with code exit status 1  
  
CircleCI received exit code 1  
```  
  
The CircleCI builds from 10 days ago and earlier worked fine for long time, screenshot from https://app.circleci.com/pipelines/github/boostorg/gil?branch=develop  
  
![image](https://user-images.githubusercontent.com/80741/105323050-f6141f00-5bc9-11eb-84c1-9f18e82e7cc0.png)  
  
The Circle CI configuration for Boost.GIL has not changed for almost two years:  
https://github.com/boostorg/gil/blob/develop/.circleci/config.yml

---

## Comment 3

> Username: grafikrobot  
> Created at: 2021-01-21 14:29:00 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764679795  

@mloskot can you add a `B2_VERBOSE=0` to your env vars in CI? Setting that will show a bit more output of what the build script is doing and why it's failing.

---

## Comment 4

> Username: mloskot  
> Created at: 2021-01-21 16:19:14 UTC  
> Updated at: 2021-01-21 16:26:49 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764762871  

@grafikrobot Done in https://github.com/boostorg/gil/commit/eef475e27a91df8c912fbf82f330d1af955743ce  
  
https://app.circleci.com/pipelines/github/boostorg/gil/142/workflows/b431df05-8be5-475f-8d2a-cc8db78aefad/jobs/16224

---

## Comment 5

> Username: sdarwin  
> Created at: 2021-01-21 16:36:16 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764775674  

In https://github.com/boostorg/boost/blob/develop/bootstrap.sh , change all instances of   
```  
CXX=   
```  
  
to   
```  
CXX= CXXFLAGS= LINKFLAGS=   
```  
  
clearing additional flags.   
  
It fixes the cases I mentioned earlier, boostorg/utility, boostorg/iterator, boostorg/detail.    For those jobs, the most noticeable difference on the failed job was CXXFLAGS="-stdlib=libc++" LINKFLAGS="-stdlib=libc++" , which were likely "leaking through" to B2, the same way CXX was.   
  
No guarantee it also fixes @mloskot's bug, they might be different cases.

---

## Comment 6

> Username: grafikrobot  
> Created at: 2021-01-21 16:48:56 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764784782  

Hm, yeah, leaking `CXXFLAGS` is likely the issue. Note `LINKFLAGS` is not used in the `build.sh` so that shouldn't affect it (no idea if the underlying compiler uses it or not though). I'll make a PR for the `CXFLAGS` change.

---

## Comment 7

> Username: grafikrobot  
> Created at: 2021-01-21 16:53:54 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-764788540  

https://github.com/boostorg/boost/pull/468

---

## Comment 8

> Username: grafikrobot  
> Created at: 2021-01-22 15:52:55 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-765502507  

boostorg/boost#468 was merged.

---

## Comment 9

> Username: mloskot  
> Created at: 2021-01-22 17:08:17 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-765558918  

https://app.circleci.com/pipelines/github/boostorg/gil/143/workflows/e71ab987-b2a3-4223-bf7f-06584ad6d776/jobs/16255/parallel-runs/0/steps/0-102 confirms the merged fix is used  
  
```  
commit 8fdf613a434f51c6c82e90caa2a221e7a2430d2b  
Merge: 77f24cf 8db75db  
Author: Glen Fernandes <glen.fernandes@gmail.com>  
Date:   Fri Jan 22 10:26:06 2021 -0500  
  
    Merge pull request #468 from grafikrobot/patch-4  
      
    Also prevent leaking CXXFLAGS from CI setups.  
```  
  
but the bootstrapping step https://app.circleci.com/pipelines/github/boostorg/gil/143/workflows/e71ab987-b2a3-4223-bf7f-06584ad6d776/jobs/16257/parallel-runs/0/steps/0-103 is still failing  
  
```  
#!/bin/bash -eo pipefail  
cd boost-root && ./bootstrap.sh -with-toolset=$TOOLSET || cat ./bootstrap.log  
  
Building B2 engine..  
> g++ -x c++ -std=c++11 check_cxx11.cpp  
./build.sh: 47: ./build.sh: g++: not found  
  
Failed to build B2 build engine  
cat: ./bootstrap.log: No such file or directory  
  
Exited with code exit status 1  
  
CircleCI received exit code 1  
```  
  
Without trying to debug it myself, I assume my issue was not related to @sdarwin 's issue.

---

## Comment 10

> Username: grafikrobot  
> Created at: 2021-01-23 02:25:43 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-765819457  

> but the bootstrapping step https://app.circleci.com/pipelines/github/boostorg/gil/143/workflows/e71ab987-b2a3-4223-bf7f-06584ad6d776/jobs/16257/parallel-runs/0/steps/0-103 is still failing  
>   
> ```  
> #!/bin/bash -eo pipefail  
> cd boost-root && ./bootstrap.sh -with-toolset=$TOOLSET || cat ./bootstrap.log  
>   
> Building B2 engine..  
> > g++ -x c++ -std=c++11 check_cxx11.cpp  
> ./build.sh: 47: ./build.sh: g++: not found  
  
At this point the script normally would move on to trying `clang++` as a compiler. I suspect the shell on that CI is behaving strangely and exiting early on command failures. I'll investigate how to forcibly put the shell into the not-exit-on-error mode.

---

## Comment 11

> Username: mloskot  
> Created at: 2021-01-23 12:56:56 UTC  
> Url: https://github.com/boostorg/build/issues/704#issuecomment-765991113  

I see. I made some trivial suggestion in https://github.com/boostorg/build/issues/705#issuecomment-765988476
