# #371 - Remove explicit instantiations for old connections in separate-build mode [Open]

> Username: anarthal  
> Created at: 2024-10-14 09:02:15 UTC  
> Updated at: 2024-10-14 09:02:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/371  

Now that `any_connection` is the way forward, these instantiations only increase build times needlessly.
