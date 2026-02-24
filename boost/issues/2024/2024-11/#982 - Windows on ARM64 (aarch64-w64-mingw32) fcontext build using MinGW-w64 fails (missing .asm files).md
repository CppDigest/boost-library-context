# #982 - Windows on ARM64 (aarch64-w64-mingw32) fcontext build using MinGW-w64 fails (missing .asm files) [Open]

> Username: brechtsanders  
> Created at: 2024-11-17 10:11:47 UTC  
> Updated at: 2024-11-17 10:11:47 UTC  
> Url: https://github.com/boostorg/boost/issues/982  

When trying to build a aarch64-w64-mingw32 build (native on Windows on ARM using this [LLVM/CLang](https://github.com/mstorsjo/llvm-mingw)) with `-DBOOST_CONTEXT_IMPLEMENTATION:STRING=fcontext` there are errors that the following files are missing:  
- `libs/context/src/asm/make_arm64_ms_pe_gas.asm`  
- `libs/context/src/asm/jump_arm64_ms_pe_gas.asm`  
- `libs/context/src/asm/ontop_arm64_ms_pe_gas.asm`  
  
However, the following files are present:  
- `libs/context/src/asm/make_arm64_aapcs_pe_armasm.asm`  
- `libs/context/src/asm/jump_arm64_aapcs_pe_armasm.asm`  
- `libs/context/src/asm/ontop_arm64_aapcs_pe_armasm.asm`  
  
Could you please provide the missing GAS versions?
