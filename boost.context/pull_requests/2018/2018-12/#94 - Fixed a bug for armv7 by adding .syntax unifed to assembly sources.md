# #94 Fixed a bug for armv7 by adding .syntax unifed to assembly sources [Merged]

> Username: the78mole  
> Created at: 2018-12-02 12:06:05 UTC  
> Updated at: 2018-12-02 16:26:18 UTC  
> Merged at: 2018-12-02 16:26:18 UTC  
> Closed at: 2018-12-02 16:26:18 UTC  
> Url: https://github.com/boostorg/context/pull/94  

I got errors compiling boost on armhf with following errors:  
  
```  
libs/context/src/asm/make_arm_aapcs_elf_gas.S: Assembler messages:  
libs/context/src/asm/make_arm_aapcs_elf_gas.S:47: Error: unshifted register required -- `bic a1,a1,#15'  
libs/context/src/asm/make_arm_aapcs_elf_gas.S:56: Error: immediate value out of range  
libs/context/src/asm/ontop_arm_aapcs_elf_gas.S: Assembler messages:  
libs/context/src/asm/ontop_arm_aapcs_elf_gas.S:49: Error: invalid register list to push/pop instruction -- `push {a1,v1-v8,lr}'  
libs/context/src/asm/ontop_arm_aapcs_elf_gas.S:75: Error: invalid register list to push/pop instruction -- `pop {a1,v1-v8,lr}'  
...skipped <pbin.v2/libs/context/build/gcc-8.2.0/release/link-static/threading-multi>libboost_context.a(clean) for lack of <pbin.v2/libs/context/build/gcc-8.2.0/release/link-static/threading-multi>asm/make_arm_aapcs_elf_gas.o...  
...skipped <pbin.v2/libs/context/build/gcc-8.2.0/release/link-static/threading-multi>libboost_context.a for lack of <pbin.v2/libs/context/build/gcc-8.2.0/release/link-static/threading-multi>asm/make_arm_aapcs_elf_gas.o...  
```  
...and fixed it by adding the directive `.syntax unified`  
  
I'm not sure that it fixes the issue or introduces other problems, but at least it compiles cleanly with gcc-8.2.0.

---

## Comment 1

> Username: the78mole  
> Created_at: 2018-12-02 14:29:11 UTC  
> Url: https://github.com/boostorg/context/pull/94#issuecomment-443511776  

Could anyone help here? I've totally no idea why my patch kills an OSX/xcode build???

---

## Comment 2

> Username: olk  
> Created_at: 2018-12-02 16:26:11 UTC  
> Url: https://github.com/boostorg/context/pull/94#issuecomment-443520506  

ty

---
