# #3 - transcation statements in async mode. [Closed]

> Username: zhuangyan-stone  
> Created at: 2020-04-14 16:09:01 UTC  
> Updated at: 2020-04-27 21:33:16 UTC  
> Closed at: 2020-04-27 21:32:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/3  

"START TRANSACTION" 、 "COMMIT"  and "ROLLBACK" . Can I execute these statements with async_query() ?

---

## Comment 1

> Username: anarthal  
> Created at: 2020-04-27 21:32:24 UTC  
> Updated at: 2020-04-27 21:33:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/3#issuecomment-620246982  

Yes, you can. These are regular SQL statements from the MySQL protocol point of view, and as such you can issue them both in sync and async mode (tested in MySQL 8.0.19).
