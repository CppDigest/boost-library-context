# #127 avoid strlen() calls by using the size-1 from GetNameByOffset [Merged]

> Username: apolukhin  
> Created at: 2022-09-01 16:04:16 UTC  
> Updated at: 2022-09-01 16:45:41 UTC  
> Merged at: 2022-09-01 16:45:37 UTC  
> Closed at: 2022-09-01 16:45:37 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/127  

Refs: https://github.com/boostorg/stacktrace/issues/122

---

## Comment 1

> Username: coveralls  
> Created_at: 2022-09-01 16:39:02 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/127#issuecomment-1234524046  

[![Coverage Status](https://coveralls.io/builds/52140992/badge)](https://coveralls.io/builds/52140992)  
  
Coverage remained the same at 93.865% when pulling **cc4d16e2adb628eabc163aacbfc0c2f639e64e94 on antoshkka/no-more-strlen** into **7c6778e9f4d43f29d215b3dd910a6fee6a3db840 on develop**.

---
