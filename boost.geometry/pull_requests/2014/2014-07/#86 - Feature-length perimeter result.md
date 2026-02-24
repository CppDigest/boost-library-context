# #86 Feature/length perimeter result [Merged]

> Username: mkaravel  
> Created at: 2014-07-02 22:44:37 UTC  
> Updated at: 2014-07-11 06:28:23 UTC  
> Merged at: 2014-07-10 21:35:26 UTC  
> Closed at: 2014-07-10 21:35:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/86  

Change the length and perimeter return type to `default_length_result<...>::type`

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-10 19:02:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/86#issuecomment-48648989  

Do you plan to add resolving of strategies - `namespace resolve_strategy`, strategy-dependent `length_result<>`, etc. Or is this PR ready for merging?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-07-10 20:51:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/86#issuecomment-48662550  

The PR is ready for merging.  
  
Both `length` and `perimeter` were not using the passed strategy to determine the return type. I kept this design, which is why both these functions, and both versions of them (with and without the strategy), return `default_length_result` now.  
  
We can of course change the return type, and/or make it strategy dependent if there is a need for that. But we discuss first if this is what we really want or not.

---
