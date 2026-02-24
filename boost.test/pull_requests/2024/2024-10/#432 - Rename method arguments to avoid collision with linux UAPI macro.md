# #432 Rename method arguments to avoid collision with linux UAPI macro [Merged]

> Username: notdanhan  
> Created at: 2024-10-15 14:14:08 UTC  
> Updated at: 2025-01-22 16:23:08 UTC  
> Merged at: 2025-01-22 16:23:07 UTC  
> Closed at: 2025-01-22 16:23:07 UTC  
> Url: https://github.com/boostorg/test/pull/432  

`tc_gen` is a macro in the linux UAPI, if included before `<boost/test/unit_test.hpp>` it causes compilation to fail.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-10-15 14:43:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/432#pullrequestreview-2369601455  

Looks reasonable; I approved the CI run.

---
