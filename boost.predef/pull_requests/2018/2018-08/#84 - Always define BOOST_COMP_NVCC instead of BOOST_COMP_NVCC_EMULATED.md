# #84 Always define BOOST_COMP_NVCC instead of BOOST_COMP_NVCC_EMULATED [Merged]

> Username: BenjaminW3  
> Created at: 2018-08-28 18:27:55 UTC  
> Updated at: 2018-09-01 09:03:04 UTC  
> Merged at: 2018-08-30 19:41:44 UTC  
> Closed at: 2018-08-30 19:41:45 UTC  
> Url: https://github.com/boostorg/predef/pull/84  

Currently BOOST_COMP_NVCC is never set but always BOOST_COMP_NVCC_EMULATED  
because the NVCC preprocessor emulates the host compiler being used  
(gcc/clang/msvc/...) which are detected earlier.  
  
The nvcc compilation process is somewhat special as can be read here:  
https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#cuda-compilation-trajectory  
The nvcc compiler precompiles the input two times. Once for the device code  
being compiled by the cicc device compiler and once for the host code  
compiled by the real host compiler. NVCC uses gcc/clang/msvc/...  
depending on the host compiler being set on the command line.  
  
Predef (as a preprocessor only lib) detects the one doing the preprocessing  
as compiler and expects it to be the one doing the real compilation.  
This is not true for NVCC which is only doing the preprocessing and which  
is using another compiler for parts of its work. So for NVCC it should be  
allowed to set BOOST_COMP_NVCC additionally to the already detected host  
compiler because both is true: It is gcc/clang/... compiling the code, but it  
is also NVCC doing th preprocessing and adding some other quirks you may  
want to detect.  
  
This behavior is similar to what boost config is doing in `select_compiler_config.hpp`.  
There the NVCC detection is not handled as a real compiler (part of the  
#if-#elif) but as additional option before the real compiler.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2018-08-30 02:05:01 UTC  
> Url: https://github.com/boostorg/predef/pull/84#issuecomment-417165310  

Two comments:  
  
1. I would highly prefer to have only one compiler detection definition. If the case is that nvcc should be detected.. Then perhaps the underlying compiler should be detected as emulated. But I don't know much about nvcc, so I will take your word on what we should do.  
2. You need to remove the commented out code and just leave the used code for me to accept anything.

---

## Comment 2

> Username: BenjaminW3  
> Created_at: 2018-08-30 16:38:58 UTC  
> Url: https://github.com/boostorg/predef/pull/84#issuecomment-417385279  

I have removed the commented out code.  
  
I would also prefer to have only one compiler being detected. However, nvcc really seems to be an exception because it is neither gcc that emulates nvcc nor the other way around.

---
