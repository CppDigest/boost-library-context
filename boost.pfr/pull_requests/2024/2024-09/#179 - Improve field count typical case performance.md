# #179 Improve field count typical case performance [Closed]

> Username: rressi-at-globus  
> Created at: 2024-09-05 11:52:49 UTC  
> Updated at: 2024-09-06 06:27:29 UTC  
> Closed at: 2024-09-06 06:27:28 UTC  
> Url: https://github.com/boostorg/pfr/pull/179  

# Description  
  
The tightest upper bound one can specify on the number of fields in a struct is sizeof(type) * CHAR_BIT. So this was previously used when performing a binary search for the field count. This upper bound is extremely loose when considering a typical large struct, which is more likely to contain a relatively small number of relatively large fields rather than the other way around. The binary search range being multiple orders of magnitude larger than necessary wouldn't have been a significant issue if each test was cheap, but they're not. Testing a field count of N costs O(N) memory and time. As a result, the initial few steps of the binary search may be prohibitively expensive.  
  
The primary optimization introduced by these changes is to use unbounded binary search, a.k.a. exponential search, instead of the typically loosely bounded binary search. This produces a tight upper bound (within 2x) on the field count to then perform the binary search with.  
  
As an upside of this change, the compiler-specific limit placed on the upper bound on the field count to stay within compiler limits could be removed.  
  
# Notes  
  
This PR has been obtained by resolving merge issues from this older PR:  
  
- https://github.com/boostorg/pfr/pull/120

---

## Comment 1

> Username: rressi-at-globus  
> Created_at: 2024-09-06 06:27:28 UTC  
> Url: https://github.com/boostorg/pfr/pull/179#issuecomment-2333336531  

Given that the original PR has resolved his merge conflicts, and that @runer112 has done further investigations, I can close this PR which is now redundant:  
- https://github.com/boostorg/pfr/pull/120

---
