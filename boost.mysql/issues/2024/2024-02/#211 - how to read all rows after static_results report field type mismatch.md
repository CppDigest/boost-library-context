# #211 - how to read all rows after static_results report field type mismatch? [Closed]

> Username: huangfeidian  
> Created at: 2024-02-01 03:07:58 UTC  
> Updated at: 2024-02-02 12:16:45 UTC  
> Closed at: 2024-02-02 12:16:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/211  

In a query I write a wrong collection name when doing, so the async_execute static_results report type mismatch.  After I ignore this error, all  async_execute afterwards report a error of sequence mismatch.   
the document says: Once you start a multi-function operation with [connection::start_execution](https://www.boost.org/doc/libs/develop/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/start_execution.html), the server immediately sends all rows to the client. You must read all rows before engaging in further operations. Otherwise, you will encounter packet mismatches, which can lead to bugs and vulnerabilities!  
But in my secenior, I just use a simple async_execute with static results. How do I read all rows? Should I just use async_read_some_rows?

---

## Comment 1

> Username: anarthal  
> Created at: 2024-02-02 11:59:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/211#issuecomment-1923665900  

Hi @huangfeidian,  
  
Type errors reported by the static interface are currently fatal. There is no way to recover from them, other than closing and opening the connection again. These include `client_errc::metadata_check_failed`, `client_errc::num_resultsets_mismatch`, `client_errc::row_type_mismatch` and `client_errc::static_row_parsing_error`. Using `start_execution` instead of `execute` won't solve the problem.  
  
That said, we could enhance the static interface to make these non-fatal. Before this, I'd like to understand your use case. When I coded this, my assumption was that these errors always indicated programming errors that required code changes, and hence recovering from these wasn't a requirement. Could you please elaborate on your use case?  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: huangfeidian  
> Created at: 2024-02-02 12:16:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/211#issuecomment-1923690938  

Thanks for you reply, I encounter this error because of silly typo of collection name. After fix the collection name typo , I just wonder any way to fail recover out of curiosity.
