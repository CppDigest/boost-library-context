# #303 restore exception state after destruction of fiber [Merged]

> Username: NaN-git  
> Created at: 2025-05-10 01:51:53 UTC  
> Updated at: 2025-05-25 14:34:48 UTC  
> Merged at: 2025-05-25 14:34:41 UTC  
> Closed at: 2025-05-25 14:34:41 UTC  
> Url: https://github.com/boostorg/context/pull/303  

In the destructor of fiber its context can be loaded. The exception state saved in `resume()`/`resume_with()` can be restored during stack unwinding after a `forced_unwind` exception is thrown, which is caught afterwards, i.e. the exception state becomes corrupted.  
  
This patch ensures that the correct exception state is restored before the destructor of the fiber returns.  
  
Fixes #302.

---

## Comment 1

> Username: NomAnor  
> Created_at: 2025-05-10 13:45:32 UTC  
> Url: https://github.com/boostorg/context/pull/303#issuecomment-2868872356  

This fixes the problem with nix in Arch Linux as referenced in the issue.

---

## Comment 2

> Username: olk  
> Created_at: 2025-05-25 14:34:48 UTC  
> Url: https://github.com/boostorg/context/pull/303#issuecomment-2907861228  

ty

---
