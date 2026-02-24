# #73 add detection for PTX architecture [Merged]

> Username: BenjaminW3  
> Created at: 2018-03-11 20:55:30 UTC  
> Updated at: 2018-06-12 12:39:44 UTC  
> Merged at: 2018-06-12 12:39:43 UTC  
> Closed at: 2018-06-12 12:39:43 UTC  
> Url: https://github.com/boostorg/predef/pull/73  

See [here](https://github.com/llvm-mirror/clang/blob/d048a9ebfa78cebf5ccd7e8788221085a880eebb/lib/Basic/Targets/NVPTX.cpp#L165-L192) for a mapping of compute capabilities given on the command line to values of the `__CUDA_ARCH__` macro.  
Futhermore, the CUDA documentation describes the [structure](http://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#virtual-architecture-identification-macro) of this macro.

---

## Comment 1

> Username: ax3l  
> Created_at: 2018-03-12 10:21:10 UTC  
> Url: https://github.com/boostorg/predef/pull/73#issuecomment-372259791  

Just for reference and complementary to the LLVM link, this is also the list for all currently supported CUDA devices with their compute capability:  
https://developer.nvidia.com/cuda-gpus

---

## Comment 2

> Username: BenjaminW3  
> Created_at: 2018-06-07 20:07:12 UTC  
> Url: https://github.com/boostorg/predef/pull/73#issuecomment-395548226  

Is there anything necessary to do before this can be merged?

---
