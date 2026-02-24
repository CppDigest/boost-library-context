# #325 fix cortex-m0 support to aapcs_elf_gas target. [Merged]

> Username: microcai  
> Created at: 2026-01-18 19:09:30 UTC  
> Updated at: 2026-02-23 07:02:53 UTC  
> Merged at: 2026-02-23 07:02:48 UTC  
> Closed at: 2026-02-23 07:02:48 UTC  
> Url: https://github.com/boostorg/context/pull/325  

some instruction used here is not available on Cortex-M0, aka, subjective to Cortex-M4. I'm using boost_fcontext on some M0 based MCU for cooperative multi tasking. Adding this #ifdef guard make sure these code runs fun on Cortex-M0 based MCU.

---

## Comment 1

> Username: olk  
> Created_at: 2026-02-23 07:02:53 UTC  
> Url: https://github.com/boostorg/context/pull/325#issuecomment-3943036283  

ty

---
