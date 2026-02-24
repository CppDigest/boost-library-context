# #291 [skip ci] Ensure fca containers have the embedded GDB pretty-printers… [Merged]

> Username: k3DW  
> Created at: 2024-10-09 03:27:00 UTC  
> Updated at: 2025-09-12 22:25:07 UTC  
> Merged at: 2024-10-09 07:54:47 UTC  
> Closed at: 2024-10-09 07:54:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/291  

… if none of the [c]foa containers have also been included. This was missed in PR #274. We don't yet have automated pretty-printer tests, so this change will have no effect on CI.

---
