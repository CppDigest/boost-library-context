# #56 - Question Regarding Prepared Statements in Multi-Threaded Envirornment [Closed]

> Username: WarrenN1  
> Created at: 2022-04-12 22:07:30 UTC  
> Updated at: 2022-05-13 12:44:32 UTC  
> Closed at: 2022-05-11 09:31:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/56  

TLDR: How do the prepared statements and connections work in a multithreaded environment?  
  
I am trying to optimize throughput and to my understanding I know mysql has low upper bound on the maximum number of connections for the application. I want to use the safety of prepared statements for a REST service using boost beast, but don't know how the connections & prepared statements for said connections in this library will interact in a multi-threaded environment given all of the examples are single threaded.

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-13 09:20:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/56#issuecomment-1097781063  

Anything based on ASIO normally treats multi-threading as a higher level concern. This is for performance reasons since most asynchronous networking applications achieve best throughput by performing all IO on a single thread.  
  
To use asio-based objects in a multi-threading environment requires that the user takes steps to avoid data races. Asio provides the `strand` executor type to help facilitate this.

---

## Comment 2

> Username: anarthal  
> Created at: 2022-04-15 14:25:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/56#issuecomment-1100140430  

As @madmongo1 pointed out, this library follows the same conventions as Boost.Asio, and all the three I/O objects are not thread-safe, i.e. you shouldn't call mutating member functions from separate threads concurrently on the same object. Note also that `prepared_statement` and `resultset` network functions trigger I/O on the `connection` object that returned them, so it's **NOT** safe to call `prepared_statement::execute` concurrently on two `prepared_statement` objects that were returned by the same connection.  
  
Please also read [this section](https://anarthal.github.io/mysql/mysql/async.html#mysql.async.sequencing) of the docs. Briefly, when you execute a query (either by calling `connection::query` or `prepared_statement::execute`, both work similarly), this is what happens:  
  
```  
     client                                server  
             ----- query request --->   
             <-------   query OK ----  
             <-------   metadata ----  
             <-------   rows --------  
             <-------   EOF ---------   
```  
The client writes a query request, and the server answers with several metadata packets (describing the fields that your query will contain), then one packet per row, and finally an EOF packet telling the client there are no more rows. When you call `connection::query` or `prepared_statement::execute`, you are writing the query request packet and reading the query OK and metadata packets. The row packets will have been sent by this time by the server, but will not be processed until you explicitly call `resultset::read_xxxx` functions. There is no way to tell which packets belong to which resultset, so you must completely read the first resultset before engaging into further operations.  
  
If you want to improve performance, we could go down the way of _query pipelining_: sending several query requests in batch, before waiting for the server to send all the response packets. This would look like:  
  
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
This would save you the round-trip time, so would be more beneficial if your latency to your server is high.  
  
Just to be clear, this **cannot be done yet** with the current library implementation. If this is something that could interest you, please let me know.  
  
Hope this helps,  
Ruben.

---

## Comment 3

> Username: anarthal  
> Created at: 2022-05-13 12:44:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/56#issuecomment-1126017483  

I've raised #75 to track pipeline mode.
