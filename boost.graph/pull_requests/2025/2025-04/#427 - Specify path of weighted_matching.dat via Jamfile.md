# #427 Specify path of weighted_matching.dat via Jamfile [Merged]

> Username: jorisvr  
> Created at: 2025-04-05 10:20:55 UTC  
> Updated at: 2025-04-30 10:33:34 UTC  
> Merged at: 2025-04-30 10:33:34 UTC  
> Closed at: 2025-04-30 10:33:34 UTC  
> Url: https://github.com/boostorg/graph/pull/427  

Test `weighted_matching_test.cpp` currently expects its input file `weighted_matching.dat` in the current working directory. As a result, testing fails when invoked via `b2` from another working directory.  
  
In previous releases, `weighted_matching_test.cpp` would silently skip all tests when it failed to open its input file. I "fixed" this in commit f98edae416607751b907f58ef47d6c2117c23b76 , thus causing the issue.  
  
This commit fixes the issue by specifying the path of `weighted_matching.dat` via the Jamfile.  
  
Fixes: #426

---

## Review 1 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-04-30 10:33:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/427#pullrequestreview-2806616849  

Thank you!!

---
