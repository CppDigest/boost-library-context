# #75 - Pipeline mode [Closed]

> Username: anarthal  
> Created at: 2022-05-13 12:43:55 UTC  
> Updated at: 2024-06-08 10:47:43 UTC  
> Closed at: 2024-06-08 10:47:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/75  

Implement client-side pipelining of requests.  
  
When executing a text query or a prepared statement, the following packet exchange occurs:  
```  
     client                                server  
             ----- query request --->   
             <-------   query OK ----  
             <-------   metadata ----  
             <-------   rows --------  
             <-------   EOF ---------   
```  
  
Currently, `connection::query` or `prepared_statement::execute` write the query request, read the OK and the metadata. Then the client needs to read all the rows until the EOF before executing another query request.  
  
It is possible to write several of these query requests at once, in batch. Doing this, we can save round-trips. Subsequent queries won't wait until a response has been written. Note that there is no special support for this neither in the protocol nor in the server. The server will process the queries sequentially, as if they had been written one by one. If one of the queries fail, subsequent queries will be executed, regardless of the result of previous queries.  
  
This would be the message exchange:  
  
```  
     client                                server  
             --- query request 1 --->   
             --- query request 2 --->   
             <-----   query 1 OK ----  
             <-----   metadata 1 ----  
             <-----   rows 1 --------  
             <-----   EOF 1 ---------   
             <-----   query 2 OK ----  
             <-----   metadata 2 ----  
             <-----   rows 2 --------  
             <-----   EOF 2 ---------   
```  
  
The client would still need to read the resultset yielded by query 1 completely before starting with query 2 resultset.  
  
Implementing this pipeline mode requires an additional, lower level interface. A possible implementation for requests is a `serializer` object with methods such as `add_query` and `add_statement_execution` that implements request serialization, then a `connection::write(serializer)` method. To read responses, we can implement methods such as `connection::read_resultset_metadata(resultset&)` and `connection::read_row(resultset&, row&)`.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-09-15 14:19:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/75#issuecomment-1721361383  

MariaDB has a bulk execution interface that may be relevant here: https://mariadb.com/kb/en/com_stmt_bulk_execute/
