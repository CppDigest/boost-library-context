# #107 Fixed compilation with -DBOOST_NO_TYPEID [Closed]

> Username: erestor  
> Created at: 2023-03-31 13:50:47 UTC  
> Updated at: 2023-11-05 23:05:47 UTC  
> Closed at: 2023-11-05 23:05:02 UTC  
> Url: https://github.com/boostorg/property_tree/pull/107  

Fixes #106

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-11-05 23:05:46 UTC  
> Url: https://github.com/boostorg/property_tree/pull/107#issuecomment-1793875313  

I used `boost::core::type_name` instead to avoid the `#ifdef`.

---
