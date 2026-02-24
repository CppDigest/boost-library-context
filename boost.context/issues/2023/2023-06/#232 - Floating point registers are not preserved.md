# #232 - Floating point registers are not preserved [Open]

> Username: lifflander  
> Created at: 2023-06-21 17:14:08 UTC  
> Updated at: 2023-08-18 04:59:51 UTC  
> Url: https://github.com/boostorg/context/issues/232  

Our project (https://github.com/DARMA-tasking/vt) uses Boost context for our task-based runtime system for scientific applications in HPC. Recently, we found a bug in a scientific application that uses our library after a context switch that we finally figured was due to not preserving nonvolatile floating point registers (in this case we were running on PPC64, but I think the problem is more prevalent). According to the POWER9 specification, FP14-FP31 are nonvolatile and must be preserved for correct context switching (https://www.ibm.com/docs/en/aix/7.2?topic=overview-register-usage-conventions).  
  
I've noticed looking back in the history that floating point preservation was dropped around v1.61. The argument for preserving in `jump_fcontext` was dropped. I'm trying to understand why this functionality was lost.  
  
@olk

---

## Comment 1

> Username: olk  
> Created at: 2023-08-18 04:59:20 UTC  
> Updated at: 2023-08-18 04:59:51 UTC  
> Url: https://github.com/boostorg/context/issues/232#issuecomment-1683354714  

Probably a re-factoring in one of the merge-requests has dropped floting point registers from preserving.  
Unfortunately I don't have access to PPC platforms.  
  
Could you provide a merge-request that fixes your problem?
