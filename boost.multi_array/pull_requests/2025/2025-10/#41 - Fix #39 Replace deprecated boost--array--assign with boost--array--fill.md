# #41 Fix #39 Replace deprecated boost::array::assign with boost::array::fill [Merged]

> Username: GreenGary  
> Created at: 2025-10-29 15:53:04 UTC  
> Updated at: 2026-02-06 01:38:16 UTC  
> Merged at: 2026-02-06 01:38:16 UTC  
> Closed at: 2026-02-06 01:38:16 UTC  
> Url: https://github.com/boostorg/multi_array/pull/41  

Fix #39 Replace deprecated boost::array::assign with boost::array::fill to avoid warnings in MSVC  
  
I did NOT verify, that this PR covers all occurrences. It is just covering what I am using in my project.

---

## Comment 1

> Username: GreenGary  
> Created_at: 2025-10-29 16:04:19 UTC  
> Url: https://github.com/boostorg/multi_array/pull/41#issuecomment-3462469040  

Hmm, I just checked CI: `'cl' is not recognized as an internal or external command,  
operable program or batch file.` does not sound like a problem related to this MR...

---
