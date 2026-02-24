# #307 CMake: Set correct assembler for MSVC ARM targets [Merged]

> Username: Greenie0701  
> Created at: 2025-07-10 12:48:35 UTC  
> Updated at: 2025-07-31 12:24:34 UTC  
> Merged at: 2025-07-31 12:24:29 UTC  
> Closed at: 2025-07-31 12:24:30 UTC  
> Url: https://github.com/boostorg/context/pull/307  

**Description:**  
- The existing configuration sets the assembly language to ASM_ARMASM for BOOST_CONTEXT_ASSEMBLER for all arm compilers.   
- This patch introduces a conditional check for MSVC to set the assembly language to ASM_MARMASM instead.  
  
**Changes:**  
  
- Added a conditional statement to check if the compiler is MSVC.  
- Set ASM_LANGUAGE to ASM_MARMASM if MSVC is detected.  
- Retained the existing setting of ASM_ARMASM for non-MSVC compilers.

---

## Comment 1

> Username: olk  
> Created_at: 2025-07-31 12:24:34 UTC  
> Url: https://github.com/boostorg/context/pull/307#issuecomment-3139756089  

ty

---
