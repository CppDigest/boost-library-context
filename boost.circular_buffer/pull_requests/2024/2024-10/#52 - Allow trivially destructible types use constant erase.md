# #52 Allow trivially destructible types use constant erase [Open]

> Username: ldeng-ustc  
> Created at: 2024-10-16 15:15:13 UTC  
> Updated at: 2024-10-16 15:15:13 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/52  

Use `boost::has_trivial_destructor` instead of `boost::is_scalar` to determine whether constant-time erasing is enabled.  
Add tests to check constant-time erasing run correctly with trivially destructible types.  
Fixes #51 .

---
