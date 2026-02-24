# #144 fixed compilation error in mfc.hpp with VS2022, MSVC 14.3 [Merged]

> Username: tobias-loew  
> Created at: 2023-04-12 07:10:26 UTC  
> Updated at: 2023-08-28 07:41:52 UTC  
> Merged at: 2023-08-28 07:41:52 UTC  
> Closed at: 2023-08-28 07:41:52 UTC  
> Url: https://github.com/boostorg/range/pull/144  

`const CObList` and `const CPtrList` still return a value from `GetHead`, `GetTail` and `GetAt`. MSVC 14.2 didn't complain about it, but 14.3 does so.  
  
It would be great if this fix could make it into 1.82, since the only "non-intrusive" way to fix this as user would be defining BOOST_RANGE_MFC_CONST_COL_RETURNS_NON_REF which affects also other collections.

---
