# #192 - Connection pool: per-operation metadata_mode [Open]

> Username: anarthal  
> Created at: 2023-12-19 15:16:56 UTC  
> Updated at: 2023-12-19 15:16:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/192  

`connection_pool` doesn't allow setting metadata mode, so there's no way to retrieve metadata strings from a pooled connection (the static interface still works, since it bypasses metadata mode).  
  
Having a per-connection metadata mode is error prone, and a leftover from the time when we didn't have the universal `execute` and `start_execution` functions. Consider a per-operation metadata mode, deprecating `meta_mode` and `set_meta_mode` from `connection`, and removing the latter functions from `any_connection`.
