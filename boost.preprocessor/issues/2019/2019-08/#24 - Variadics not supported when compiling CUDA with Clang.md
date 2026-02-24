# #24 - Variadics not supported when compiling CUDA with Clang [Closed]

> Username: psalz  
> Created at: 2019-08-31 10:29:15 UTC  
> Updated at: 2020-07-15 14:13:56 UTC  
> Closed at: 2020-07-15 14:13:56 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24  

Yet another issue on CUDA; this time however when [compiling with Clang](https://llvm.org/docs/CompileCudaWithLLVM.html), i.e., not involving NVCC at all. I think in this case it is safe to enable `BOOST_PP_VARIADICS`; I have successfully worked around this with  
  
```cpp  
#if defined(__clang__) && defined(__CUDA__)  
#define BOOST_PP_VARIADICS 1  
#endif  
#include <boost/preprocessor/facilities/overload.hpp>   
```  
  
I realize there have been some issues in the past figuring out variadics support of NVCC, which is why it is currently disabled whenever `__CUDA__` is defined. Might I however suggest to either also check that `__clang__` is not defined (which however could be problematic if Clang is set as the nvcc host compiler - not sure), or to instead check for `__NVCC__`? [Here's more information on how to detect Clang CUDA vs NVCC](https://llvm.org/docs/CompileCudaWithLLVM.html#detecting-clang-vs-nvcc-from-code).

---

## Comment 1

> Username: eldiener  
> Created at: 2019-08-31 19:27:27 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-526859156  

I appreciate the link and bringing up this issue. I have made a change locally on the 'develop' branch to fix this but I really need some example code of clang compiling CUDA code to make sure my change is correct before I push it to 'develop' in the latest Boost.. Can you provide something like this with an explanation of what I need, aside from clang, to test it locally on my machine ?

---

## Comment 2

> Username: psalz  
> Created at: 2019-09-02 09:10:37 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-527069346  

Sure, here you go, `boost_pp_test.cu`:  
  
```CUDA  
#include <cstdio>  
  
#include <cuda.h>  
  
#if defined(__clang__) && defined(__CUDA__)  
//#define BOOST_PP_VARIADICS 1  
#endif  
  
#include <boost/preprocessor/facilities/overload.hpp>  
  
__global__ void my_kernel(int* x) {  
    *x += 1295;  
}  
  
int main() {  
#if BOOST_PP_OVERLOAD()  
    printf("PP Variadics enabled\n");  
#endif  
  
    // Just to make sure we're actually compiling CUDA  
    int* x;  
    cudaMalloc((void**)&x, sizeof(int));  
    int value = 42;  
    cudaMemcpy(x, &value, sizeof(int), cudaMemcpyHostToDevice);  
    my_kernel<<<1, 1>>>(x);  
    cudaMemcpy(&value, x, sizeof(int), cudaMemcpyDeviceToHost);  
    cudaFree(x);  
    printf("Result: %d\n", value);  
  
    return 0;  
}  
```  
  
Compile with  
`clang++-8 -I<path/to/boost_1_71_0> -x cuda --cuda-gpu-arch=sm_52 -o boost_pp_test boost_pp_test.cu -lcudart_static -ldl -lrt -lpthread`  
  
I think any newer Clang (6+) and CUDA (9+) should work, although I only tested this with Clang 8 and CUDA 10.0.

---

## Comment 3

> Username: eldiener  
> Created at: 2019-09-08 03:32:20 UTC  
> Updated at: 2019-09-08 23:14:41 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-529166275  

The problem I need to solve is that I want to run the test if clang is the compiler and if CUDA is installed, else I do not want to run the test. I can always know if clang is the compiler, because \_\_clang\_\_ will be defined in the preprocessor, but I do not know how I can determine if CUDA is installed at compile time. Also If CUDA is installed do not I need to know the path to its header and libraries ? Or do I assume that this is in some standard place on Linux and that I should not be testing this on Windows ?

---

## Comment 4

> Username: psalz  
> Created at: 2019-09-09 08:30:59 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-529356667  

I'm not sure I understand - why would you need to know whether CUDA is installed? Doesn't it suffice to know whether the user wants to compile CUDA code (which could be detected by checking for `__CUDA__`)?

---

## Comment 5

> Username: eldiener  
> Created at: 2019-09-09 16:52:22 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-529569496  

If I add a test to the Boost PP tests, which is run each time the Boost PP tests are run, the test will fail if CUDA is not installed. It should not be a requirement of Boost PP tests that CUDA is installed.

---

## Comment 6

> Username: psalz  
> Created at: 2019-09-10 14:06:55 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-529951921  

Ah, I see, you are talking about unit tests (I thought you meant `#if` preprocessor tests). Unfortunately I don't know anything about Boost's Jam test setup, so I don't think I can be of much help here. I wouldn't make any assumptions about CUDA's install location or Clang's ability to find it though, because I think there are some inconsistencies across distributions. Regarding Windows I have no idea, although I know that Clang 8 doesn't even support CUDA compilation on Windows (but I think it will be supported in Clang 9).  
  
But: What if your test doesn't contain any CUDA code at all, and is just compiled as such, and only until the preprocessor stage is finished (i.e., `-E`)? Then I think you would neither need the CUDA headers nor any libraries (such as `cudart_static`).

---

## Comment 7

> Username: eldiener  
> Created at: 2019-09-14 02:31:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-531440792  

I have created a preprocessor test which merely attempts to establish the fact that if clang compiles a source file ending in .cu, then BOOST_PP_VARIADICS will be 1. The actual code in the test has no CUDA implementation, and the test is marked 'explicit' so it does not run with the normal preprocessor tests unless it is explicitly invoked by its Boost Build alias designation. But I think I still need to pass a --cuda-gpu-arch=xxx parameter to the compile so that the compilation will not fail because of the lack of such a parameter. How do I decide what is the minimum valid value for such a test so that it will at least attempt to compile when explicitly invoked on various platform where clang compiling CUDA could exist. I noticed you used in your example 'sm_52'. Is this valid enough for all situations, when no CUDA code is actually invoked ? Or if CUDA code is not actually invoked does it matter what the value it is as long as it is a valid value and 'sm_52' is a valid value ?

---

## Comment 8

> Username: eldiener  
> Created at: 2019-09-14 14:34:14 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-531484413  

I have pushed a fix for your issue to the 'develop' branch. To test out the fix using the preprocessor tests you can run:  
  
`b2 clang-cuda toolset=clang`  
  
in the preprocessor test directory. Otherwise you can simply compile some cuda code with clang and see that variadic macro support in Boost PP is now turned on for that situation.

---

## Comment 9

> Username: psalz  
> Created at: 2019-09-27 13:15:38 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-535934235  

Hi, sorry for the late response, I was unavailable for a couple of days.  
  
> To test out the fix using the preprocessor tests you can run:  
  
I couldn't get this working, maybe I'm doing something wrong: I first cloned the main boost repository (develop branch) with all submodules. Then I ran `./bootstrap.sh --with-toolset=clang` and subsequently changed into the proprocessor tests directory. From there I tried running `../../../b2 clang-cuda toolset=clang`, which gives me the error `error: "cuda" is not a known subfeature value of <toolset>clang`.  
  
> Otherwise you can simply compile some cuda code with clang and see that variadic macro support in Boost PP is now turned on for that situation.  
  
That I did, and it seems to work!  
  
> Or if CUDA code is not actually invoked does it matter what the value it is as long as it is a valid value and 'sm_52' is a valid value ?  
  
That's a bit tricky. `sm_52` is just my go-to value, but this might break compilation with some older versions of Clang and/or older CUDA installs. It [looks like](https://github.com/llvm-mirror/clang/blob/822f64eae96c73035a0890db1614d4ce2b792fa4/include/clang/Basic/Cuda.h#L37) `sm_20` is the lowest supported device architecture (which I see you already adopted in your change), and presumably this is supported by the first version of Clang that supported CUDA compilation (which according to [this](https://gitlab.kitware.com/cmake/cmake/issues/16586) seems to be 3.9). Passing `sm_20` will however not work with newer CUDA installations; Clang prints an error that it requires CUDA 7.0 - 8.0. When you only run the preprocessor you can disable this check/error by additionally passing `-nocudalib --no-cuda-version-check`. So in total it would be `clang++ -x cuda --cuda-gpu-arch=sm_20 -nocudalib --no-cuda-version-check -E`. Unfortunately I again don't know when these command line parameters were added.

---

## Comment 10

> Username: eldiener  
> Created at: 2019-09-27 17:52:40 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-536037423  

I have updated the 'develop' branch again with a correctly named clang CUDA test. The test's name is now 'test_clang_cuda'. I also added your suggested extra parameters for the test, although I could not find them mentioned anywhere in the clang CUDA doc. Please pull the latest from the preprocessor 'develop' branch and try again the clang CUDA test with:  
  
`../../../b2 test_clang_cuda toolset=clang  
`  
  
I am glad that your other attempt found that variadic macro support is now turned on when you compile your cuda files with clang.

---

## Comment 11

> Username: psalz  
> Created at: 2019-09-30 07:58:14 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-536448587  

Now I'm getting this:  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
/home/psalz/OSS/boost/tools/build/src/build/generators.jam:1127: in ensure-type from module generators  
error: target { clang_cuda.cu. } has no type  
/home/psalz/OSS/boost/tools/build/src/build/generators.jam:1386: in generators.construct from module generators  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:1614: in construct from module object(typed-target)@6335  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:1363: in class@basic-target.generate from module object(typed-target)@6335  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:812: in generate-really from module object(main-target)@6418  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@6418  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:933: in targets.generate-from-reference from module targets  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:1276: in generate-dependencies from module object(alias-target-class)@6336  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:1333: in class@basic-target.generate from module object(alias-target-class)@6336  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:812: in generate-really from module object(main-target)@6419  
/home/psalz/OSS/boost/tools/build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@6419  
/home/psalz/OSS/boost/tools/build/src/build-system.jam:797: in load from module build-system  
/home/psalz/OSS/boost/tools/build/src/kernel/modules.jam:295: in import from module modules  
/home/psalz/OSS/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/home/psalz/OSS/boost/boost-build.jam:17: in module scope from module  
```  
  
> I also added your suggested extra parameters for the test, although I could not find them mentioned anywhere in the clang CUDA doc.  
  
Yes I also only found them because they were suggested by Clang's CLI error output!

---

## Comment 12

> Username: eldiener  
> Created at: 2019-10-03 09:44:41 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-537871537  

I updated 'develop' again, which eliminated the Boost Build error. However when I try running the test_clang_cuda test using clang-9.0 in Windows or in Linux, the test itself fails because __CUDACC__ is not being defined, despite compiling a CUDA file. So despite the documentation at https://llvm.org/docs/CompileCudaWithLLVM.html#detecting-clang-vs-nvcc-from-code it appears that just compiling a source file as CUDA does not cause __CUDACC__ to be defined. Something else in the source file is needed. I have asked about this in the clang users newsgroup but have yet to receive any answer.

---

## Comment 13

> Username: psalz  
> Created at: 2020-07-15 14:13:56 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/24#issuecomment-658793536  

Closing as I just realized this landed in 1.73. Very happy to see it, thank you! :-).
