# #71 Add support for detecting CUDA [Merged]

> Username: BenjaminW3  
> Created at: 2018-03-11 14:08:26 UTC  
> Updated at: 2018-05-13 15:21:13 UTC  
> Merged at: 2018-05-13 15:21:13 UTC  
> Closed at: 2018-05-13 15:21:13 UTC  
> Url: https://github.com/boostorg/predef/pull/71  

This adds language detection for CUDA.  
  
The NVIDIA nvcc compiler defines [`__CUDACC__`](http://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#nvcc-identification-macro).  
clang as CUDA compiler defines [`__CUDA__`](https://llvm.org/docs/CompileCudaWithLLVM.html#detecting-clang-vs-nvcc-from-code).  
The documentation mentions that it should also define `__CUDACC__` as well, but I could not find it [in the source code](https://github.com/llvm-mirror/clang/blob/2b1d36bee69eaf8897af89d53630804f9f66badd/lib/Frontend/InitPreprocessor.cpp#L466-L467) .

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2018-03-11 16:03:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/predef/pull/71#pullrequestreview-102887002  

Looks good.. But since there's a new make macro. Need to add a test for it here https://github.com/boostorg/predef/blob/develop/test/make.cpp

---

## Comment 2

> Username: BenjaminW3  
> Created_at: 2018-03-11 16:04:56 UTC  
> Url: https://github.com/boostorg/predef/pull/71#issuecomment-372126461  

Thanks. Will add it.

---
