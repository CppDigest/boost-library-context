# #7 Suppress MSVC warning C4800 about assigning from int to bool [Closed]

> Username: lanurmi  
> Created at: 2019-11-07 07:21:15 UTC  
> Updated at: 2022-04-16 12:31:12 UTC  
> Closed at: 2022-04-16 12:31:12 UTC  
> Url: https://github.com/boostorg/crc/pull/7  

warning C4800: "'int' : forcing value to bool 'true' or 'false' (performance warning)  
  
Fix by double-negating the expression with !!.  
  
Similar to PR #2, but not the same piece of code.

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-04-16 12:31:12 UTC  
> Url: https://github.com/boostorg/crc/pull/7#issuecomment-1100653467  

Should be addressed in 1.80 by 96daae9fcdf965298077ed12a9304ea24713bb40

---
