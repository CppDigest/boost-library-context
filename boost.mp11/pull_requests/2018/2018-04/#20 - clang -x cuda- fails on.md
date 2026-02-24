# #20 clang -x cuda: fails on >>> [Closed]

> Username: ax3l  
> Created at: 2018-04-05 17:00:01 UTC  
> Updated at: 2019-07-31 13:41:39 UTC  
> Closed at: 2018-04-26 17:46:35 UTC  
> Url: https://github.com/boostorg/mp11/pull/20  

The latest `trunk` (pre-7.0) of `clang` and all previous releases fail compiling a simple:  
```C++  
#include <boost/mp11.hpp>  
  
int main()  
{}  
```  
  
when compiling for cuda C++: `clang -x cuda`.  
  
The reason lies in some mismatch, probably between CUDA kernel starts parameters `>>>` and closing templates in combination with variadic template arguments.  
  
Reported in clang here: https://bugs.llvm.org/show_bug.cgi?id=37013  
  
This PR fixes the issue by just putting spaces in, conservatively, all places with triple+ `>` closing template brackets.

---

## Comment 1

> Username: ax3l  
> Created_at: 2018-04-25 09:52:12 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384230438  

@pdimov @glenfe can you please take a look at this fix? Is this the right way to contribute here?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-04-25 09:56:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384231634  

It is.  
  
I don't mind applying this fix to the headers proper (although I can't guarantee that future changes will not cause the problem to reappear), but is it necessary to touch the tests, let alone the docs?

---

## Comment 3

> Username: ax3l  
> Created_at: 2018-04-25 10:03:09 UTC  
> Updated_at: 2018-04-25 10:04:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384233444  

I touched the tests because one would want to test those.  
  
Doc examples can be kept, if one supports CUDA one can take care of that.  
  
> although I can't guarantee that future changes will not cause the problem to reappear  
  
Yes, for that reason I tried to create a PR for you adding coverage but didn't yet get it done: https://github.com/ax3l/mp11/tree/feature/cuda (Travis: https://travis-ci.org/ax3l/mp11/builds/363104517)

---

## Comment 4

> Username: ax3l  
> Created_at: 2018-04-25 10:52:26 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384246049  

@pdimov I removed the test and docs changes for now.  
If we need them later, I archived them in https://github.com/ax3l/mp11/tree/fix-clangCudaBracketsAll

---

## Comment 5

> Username: ax3l  
> Created_at: 2018-04-25 14:01:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384297693  

@pdimov looks like the current `develop` branch does not build with gcc 7.3.0?

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-04-25 14:12:56 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384301619  

On it.

---

## Comment 7

> Username: ax3l  
> Created_at: 2018-04-25 14:23:55 UTC  
> Updated_at: 2018-04-25 14:24:42 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384305408  

As a side note: the problem I experience while [adding CUDA to the CI](https://github.com/ax3l/mp11/commit/edc6401615a71b7edcf8aedc21bd9b14c9f04227) is that both `nvcc` and `clang++ -x cuda` (clang's CUDA-C++ frontend) are not known *tools* in boost.build: https://github.com/boostorg/build/tree/develop/src/tools  
  
Is there a way to run tests somehow outside of Boost.build for the CI?

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-04-25 14:37:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384310190  

A search turns up https://lists.boost.org/boost-build/2008/07/19764.php. I'll ask Emil for the status.  
  
I'm also experimenting with the Clang part, will let you know. The boostorg Travis is slow.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-04-25 17:16:43 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384364967  

g++ 7 should be fixed now. I tried to pass the flags you had (`-x cuda -nocudainc -nocudalib`) here: https://travis-ci.org/boostorg/mp11/jobs/371058770 but the C++ feature detection doesn't seem to take hold for some reason. Interestingly, when I try  
  
```  
b2 test toolset=clang cxxstd=11,14,1z cxxflags="-x cuda -nocudainc -nocudalib"  
```  
  
locally, it works... somewhat, I get errors like  
  
```  
error: unable to open output file '/cygdrive/c/Users/PETERD~1/AppData/Local/Temp/test\mp_cond_sf-c74fa5.s': 'No such file or directory'  
1 error generated.  
```  
  
Not sure what happens here, Cuda is not my forte. :-)

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-04-26 10:59:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384598857  

OK, I added a Travis job (on the feature/clang-x-cuda branch) that just does  
  
```  
clang++-5.0 -I . -c -x cuda -nocudainc -nocudalib -std=c++11 libs/mp11/test/mp11.cpp  
```  
  
This failed with `>>>` related errors (good) and these disappeared after your commit (good too), but it still fails with  
  
```  
/usr/lib/llvm-5.0/lib/clang/5.0.2/include/cuda_wrappers/new:37:1: error: unknown type name '__device__'  
__device__ inline void *operator new(__SIZE_TYPE__, void *__ptr) CUDA_NOEXCEPT {  
^  
```  
  
https://travis-ci.org/boostorg/mp11/jobs/371493574  
  
Any idea how I can fix this?

---

## Comment 11

> Username: ax3l  
> Created_at: 2018-04-26 12:00:41 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384615405  

Very nice! I tried it as well with `b2` but could not trigger the fail properly - yes, testing without boost.test is a good idea in your branch!  
  
`__host__` and `__device__` are just the additional qualifiers CUDA adds for functions (and variables). Usually, the [`cuda.h` header](https://gist.github.com/hughperkins/6cf34c12846e3a4995f7a4c6c51d79e2) will bring them in, but Travis' CUDA 5.5 is too ancient to be useful here (currently supported are CUDA 7.0-8.0).  
  
I tried defining them manually with `-D__device__="__attribute__((device))" -D__host__="__attribute__((host))"` but there seems to be more to it. So I would suggest we do a manual CUDA SDK install for the test. I will add a commit on yours, replacing `-nocudainc -nocudalib` with `--cuda-path`

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-04-26 12:50:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384629333  

Defining `__device__` helps, thanks:  
  
```  
$ clang++-5.0 -I . -c -x cuda -nocudainc -nocudalib --cuda-gpu-arch=sm_30 -D__device__="__attribute__((device))" -std=c++11 libs/mp11/test/mp11.cpp  
clang: error: unable to execute command: Executable "ptxas" doesn't exist!  
clang: error: ptxas command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 13

> Username: ax3l  
> Created_at: 2018-04-26 14:01:19 UTC  
> Updated_at: 2018-04-26 15:04:38 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384651728  

found it, will update the PR to add testing :)  
  
*update:* from your error I see that we will still need the CUDA Toolkit, `ptxas` comes with it.

---

## Review 14 [Commented]

> Username: ax3l  
> Created_at: 2018-04-26 14:05:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/20#pullrequestreview-115589849  

📁 .travis.yml

```diff
 189 |+         - wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_375.26_linux-run
 190 |+         - sh cuda_8.0.61_375.26_linux-run --tar xf ./InstallUtils.pm
 191 |+         - export PERL5LIB=$(pwd):$PERL5LIB
```

> Username: ax3l  
> Created_at: 2018-04-26 14:05:01 UTC  
> Updated_at: 2018-04-26 15:47:15 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#discussion_r184401607  

note: these two lines are a work-around for an install issue with the runfiles in CUDA 8


---

## Comment 15

> Username: pdimov  
> Created_at: 2018-04-26 14:21:46 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384658831  

I got it to work with the default Travis SDK: https://travis-ci.org/boostorg/mp11/jobs/371559780

---

## Comment 16

> Username: ax3l  
> Created_at: 2018-04-26 14:32:32 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384662636  

uh, nice as well!  
Be aware you do not need to add `-D__device__="__attribute__((device))"` if we have a proper cuda install via `--cuda-path`.  
  
I think let us use an officially supported CUDA release, so we can add more tests if needed that actually compile template meta functions inside a device function later on.

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-04-26 17:46:35 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-384728849  

I merged `feature/clang-x-cuda` to `develop`, which includes the patch. I'll leave the Travis job as is for now.

---

## Comment 18

> Username: ax3l  
> Created_at: 2019-07-31 13:40:17 UTC  
> Updated_at: 2019-07-31 13:41:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/20#issuecomment-516853522  

Wonderful, for newer versions of clang (potentially 9+?), this will also be fixed upstream :)  
https://bugs.llvm.org/show_bug.cgi?id=37013  
https://reviews.llvm.org/D61396

---
