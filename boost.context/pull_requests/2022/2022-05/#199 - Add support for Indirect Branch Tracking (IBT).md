# #199 Add support for Indirect Branch Tracking (IBT) [Merged]

> Username: PeterYang12  
> Created at: 2022-05-13 07:39:09 UTC  
> Updated at: 2022-05-16 08:39:30 UTC  
> Merged at: 2022-05-16 08:39:25 UTC  
> Closed at: 2022-05-16 08:39:25 UTC  
> Url: https://github.com/boostorg/context/pull/199  

Indirect Branch Tracking(IBT) is part of Intel's Control-Flow  
Enforcement Technology(CET). IBT is hardware based, forward edge  
Control-Flow-Integrity mechanism where any indirect CALL/JMP must  
target an ENDBR instruction or suffer #CP.  
  
This commit inserts endbr64 instruction in assembly to support IBT.  
  
Signed-off-by: PeterYang12 <yuhan.yang@intel.com>  
Signed-off-by: Chen, Hu <hu1.chen@intel.com>

---

## Comment 1

> Username: olk  
> Created_at: 2022-05-13 09:32:28 UTC  
> Updated_at: 2022-05-13 09:33:58 UTC  
> Url: https://github.com/boostorg/context/pull/199#issuecomment-1125847494  

causes compilation error:  
  
make_x86_64_sysv_elf_gas.S:76: Error: no such instruction: `endbr64'  
  
the architecture needs to be tested if endbr64 is supported

---

## Comment 2

> Username: PeterYang12  
> Created_at: 2022-05-16 08:03:01 UTC  
> Updated_at: 2022-05-16 08:03:12 UTC  
> Url: https://github.com/boostorg/context/pull/199#issuecomment-1127355438  

> causes compilation error:  
>   
> make_x86_64_sysv_elf_gas.S:76: Error: no such instruction: `endbr64'  
>   
> the architecture needs to be tested if endbr64 is supported  
  
The compilation error has been fixed.

---

## Comment 3

> Username: olk  
> Created_at: 2022-05-16 08:39:30 UTC  
> Url: https://github.com/boostorg/context/pull/199#issuecomment-1127392613  

ty

---
