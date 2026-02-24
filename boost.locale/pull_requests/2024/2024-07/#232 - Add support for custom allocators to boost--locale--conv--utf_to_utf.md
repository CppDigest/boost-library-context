# #232 Add support for custom allocators to boost::locale::conv::utf_to_utf  [Merged]

> Username: Flamefire  
> Created at: 2024-07-02 17:03:25 UTC  
> Updated at: 2024-07-06 06:04:03 UTC  
> Merged at: 2024-07-05 23:13:47 UTC  
> Closed at: 2024-07-05 23:13:47 UTC  
> Url: https://github.com/boostorg/locale/pull/232  

Add new optional parameter such that the use of custom allocators is supported by `boost::locale::conv::utf_to_utf.`  
  
Based on (stale) PR #231 with  
  
- Fixed formatting  
- Add tests  
- Allow passing an allocator instance to string overload  
- Allow passing allocator instances without passing the method  
  
Closes #231

---
