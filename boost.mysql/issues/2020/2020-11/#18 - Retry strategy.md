# #18 - Retry strategy [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:34:12 UTC  
> Updated at: 2024-08-10 16:15:07 UTC  
> Closed at: 2024-08-10 16:15:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/18  

Investigate what retry strategies does the MySQL C driver offer and consider implementing them here.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:15:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/18#issuecomment-2282198069  

`connection` is definitely not the right place for this. We already have retries built into `connection_pool`.
