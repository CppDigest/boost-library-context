# #7 Avoid class/struct mismatch in forward declaration and implementation [Closed]

> Username: jhunold  
> Created at: 2016-10-07 08:26:21 UTC  
> Updated at: 2018-11-15 05:38:04 UTC  
> Closed at: 2018-11-15 05:38:04 UTC  
> Url: https://github.com/boostorg/exception/pull/7  

set_info_rv is forwarded as "struct set_info_rv..." in exception.hpp and  
was implemented as "class set_info_rv...". This causes warnings in msvc and  
clang with -Weverything at least.

---

## Comment 1

> Username: eldruin  
> Created_at: 2016-10-27 05:53:49 UTC  
> Url: https://github.com/boostorg/exception/pull/7#issuecomment-256553025  

The fix in commit e1378f7aef652e8c88edf5986bad6b6c645bfe2e is not enough. The further changes from this PR are necessary at least on MSVC12.

---
