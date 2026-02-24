# #859 Avoid unnecessary copy value and map.find in lru_cache [Open]

> Username: cheerconi  
> Created at: 2021-01-15 10:13:42 UTC  
> Updated at: 2021-01-16 03:47:00 UTC  
> Url: https://github.com/boostorg/compute/pull/859  

The operator[] is a find-or-add operator. Use m_map.emplace to avoid add operation. And use i->second.second to avoid find operation.

---

## Comment 1

> Username: coveralls  
> Created_at: 2021-01-16 03:46:59 UTC  
> Url: https://github.com/boostorg/compute/pull/859#issuecomment-761328893  

[![Coverage Status](https://coveralls.io/builds/36371463/badge)](https://coveralls.io/builds/36371463)  
  
Coverage decreased (-0.006%) to 84.013% when pulling **d0e7f5aaf7dbbe34f8a51360ec3bb52fbf789b42 on cheerconi:master** into **36c89134d4013b2e5e45bc55656a18bd6141995a on boostorg:master**.

---
