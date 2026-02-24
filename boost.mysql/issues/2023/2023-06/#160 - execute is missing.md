# #160 - execute is missing [Closed]

> Username: Mecanik  
> Created at: 2023-06-07 04:13:28 UTC  
> Updated at: 2023-06-07 09:05:16 UTC  
> Closed at: 2023-06-07 09:05:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/160  

I`m using Boost 1.82 with the included MySQL, however the .execute() function is missing which is specified in the docs. Are the docs outdated or the code? Thanks.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-06-07 08:24:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/160#issuecomment-1580186771  

Actually the docs are too new - `execute()` is a newer, more general function that will be available in 1.83. execute() is in master but not in 1.82.  
  
Docs for 1.82 are here: https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/index.html  
  
You can achieve the same functionality in 1.82 using `connection::query` and `connection::execute_statement`.

---

## Comment 2

> Username: Mecanik  
> Created at: 2023-06-07 09:05:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/160#issuecomment-1580253539  

Thank you!
