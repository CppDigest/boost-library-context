# #312 [AArch64][BTI] Add BTI hint + GNU property to fcontext trampolines  [Merged]

> Username: zaydr-al  
> Created at: 2025-08-15 22:58:07 UTC  
> Updated at: 2025-08-26 11:22:28 UTC  
> Merged at: 2025-08-26 11:22:23 UTC  
> Closed at: 2025-08-26 11:22:23 UTC  
> Url: https://github.com/boostorg/context/pull/312  

**Add BTI hint + GNU property to fcontext trampolines**   
  
The AArch64 fcontext trampolines (jump_fcontext, make_fcontext, ontop_fcontext) are indirect-entry-points. On BTI-enforcing systems they must begin with a BTI or the first resume can trap with SIGILL.  
  
Insert `bti c` (hint #34) at each entry under `__ARM_FEATURE_BTI_DEFAULT`, and emit `GNU_PROPERTY_AARCH64_FEATURE_1_BTI` from each AArch64 assembly file so linkers map the DSO with PROT_BTI.  
  
Scope: ELF/GAS AArch64 trampolines only; +4 bytes per entry  
  
Fixes [#308](https://github.com/boostorg/context/issues/308)  
  
Notes  
- This change is orthogonal to the separate LLVM landing-pad ``bti j`` [issue #149267](https://github.com/llvm/llvm-project/issues/149267); exception paths built with affected Clang toolchains will still require the upstream fix.

---

## Comment 1

> Username: olk  
> Created_at: 2025-08-17 08:19:47 UTC  
> Url: https://github.com/boostorg/context/pull/312#issuecomment-3194221084  

Did you successfully run the unit tests?

---

## Comment 2

> Username: zaydr-al  
> Created_at: 2025-08-18 19:43:42 UTC  
> Url: https://github.com/boostorg/context/pull/312#issuecomment-3198185845  

I ran `libs/context/test` on AL2023/Graviton after a clean build: **all tests pass on GCC**. With **clang-15** they also pass when linking against **libgcc**.  
Happy to attach logs if helpful.

---

## Comment 3

> Username: olk  
> Created_at: 2025-08-26 11:22:28 UTC  
> Url: https://github.com/boostorg/context/pull/312#issuecomment-3223754409  

ty

---
