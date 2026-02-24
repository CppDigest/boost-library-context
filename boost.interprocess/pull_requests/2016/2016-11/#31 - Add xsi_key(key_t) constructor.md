# #31 Add xsi_key(key_t) constructor [Merged]

> Username: justinbrewer  
> Created at: 2016-11-05 06:34:20 UTC  
> Updated at: 2016-11-12 23:44:01 UTC  
> Merged at: 2016-11-12 23:43:45 UTC  
> Closed at: 2016-11-12 23:43:45 UTC  
> Url: https://github.com/boostorg/interprocess/pull/31  

Allow user to specify an exact value for `xsi_key`. New constructor is marked as explicit to avoid ambiguity with `shmid`, since `key_t` is actually `int`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-11-12 23:44:01 UTC  
> Url: https://github.com/boostorg/interprocess/pull/31#issuecomment-260156236  

Thanks for the patch.

---
