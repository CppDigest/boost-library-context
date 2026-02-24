# #76 - Docs correction: pipeline mode [Closed]

> Username: anarthal  
> Created at: 2022-05-13 13:13:00 UTC  
> Updated at: 2022-12-15 18:22:34 UTC  
> Closed at: 2022-12-15 18:22:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/76  

https://anarthal.github.io/mysql/mysql/resultsets.html#mysql.resultsets.complete states a protocol limitation, but there is a library limitation, too (we could do a better job and implement a degree of pipelining). #75 addresses implementing pipelining. Until this happens, correct this section to state what the library limitations are.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-12-15 18:22:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/76#issuecomment-1353524016  

Done as of bf4071f37062bd180a495fc73241becb32aa2c6e.
