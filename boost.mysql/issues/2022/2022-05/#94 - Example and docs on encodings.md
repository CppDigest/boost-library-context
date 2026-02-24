# #94 - Example and docs on encodings [Closed]

> Username: anarthal  
> Created at: 2022-05-19 21:37:01 UTC  
> Updated at: 2022-12-16 11:59:21 UTC  
> Closed at: 2022-12-16 11:59:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/94  

Character encoding is something that causes confusion. [This section](https://anarthal.github.io/mysql/mysql/types.html) talks about it briefly, but it's not enough. Add a section and possibly an example.  
  
Sample question:  
  
If i have a table with three columns, one in utf8, the other one in utf-16, and the last one in iso8859-15, what will the returned string_view contain? And if I'm filtering in a WHERE clause, what encoding should the WHERE clause have? Is it the same in text queries and in prepared statements?

---

## Comment 1

> Username: ddevienne  
> Created at: 2022-05-20 06:45:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/94#issuecomment-1132534162  

Is that even possible, to have different encodings per-column or per-table?  
PostgreSQL and Oracle has a single per-DB encoding for storage. And another for each session.  
It's typically highly recommended to use UTF-8 (or another lossless UTF-X one) for storage.  
Then each client/session is free to use its own encoding, at the (opt-in) risk of non-lossless conversions.
