# #118 queue: allow non-trivially moveable types [Merged]

> Username: praetorian20  
> Created at: 2025-05-18 14:09:01 UTC  
> Updated at: 2025-05-19 07:44:46 UTC  
> Merged at: 2025-05-19 07:44:46 UTC  
> Closed at: 2025-05-19 07:44:46 UTC  
> Url: https://github.com/boostorg/lockfree/pull/118  

Restores the requirements prior to commit 6a70a658 which replaced boost::has_trivial_assign with std::is_trivially_assignable.  
  
The latter trait requires both trivial copy and move assignment while the original only required trivial copy assignment. Since the lockfree queue doesn't require trivial move assignment, the requirement is being reverted back to the original.  
  
Also updated the documentation to clarify only trivial copy assignment is needed.  
  
Fixes #117

---

## Comment 1

> Username: timblechmann  
> Created_at: 2025-05-19 07:44:38 UTC  
> Url: https://github.com/boostorg/lockfree/pull/118#issuecomment-2889968231  

@praetorian20, many thanks for the patch!

---
