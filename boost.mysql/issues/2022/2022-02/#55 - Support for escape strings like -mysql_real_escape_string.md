# #55 - Support for escape strings like "mysql_real_escape_string" [Closed]

> Username: clocky-dev  
> Created at: 2022-02-15 16:17:05 UTC  
> Updated at: 2022-03-22 18:30:58 UTC  
> Closed at: 2022-03-22 18:30:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/55  

Is there a way how to escape a single string?  
  
Can I use prepared statement for async queries?

---

## Comment 1

> Username: anarthal  
> Created at: 2022-02-15 17:55:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/55#issuecomment-1040596550  

Hi,  
  
There is currently no equivalent to `mysql_real_escape_string` (which performs client-side escaping).  
  
You can achieve similar results with server-side escaping, via prepared statements. You can read more about prepared statement support [here](https://anarthal.github.io/mysql/mysql/prepared_statements.html). Prepared statements can be used with asynchronous code, by using `connection::async_prepare_statement` instead of `connection::prepare_statement` and `prepared_statement::async_execute` instead of `prepared_statement::execute`.  
  
Please let me know if this solves your use case.  
  
Cheers,  
Rubén.
