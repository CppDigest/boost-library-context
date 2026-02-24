# #182 - One connection async_excute in a loop [Closed]

> Username: venjin  
> Created at: 2023-11-16 15:34:37 UTC  
> Updated at: 2023-11-24 02:04:24 UTC  
> Closed at: 2023-11-24 02:04:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/182  

Support one connection async_excute in a loop?  
code like:  
```cpp  
for(int i=0; I<100; i++)  
{  
    conn.async_excute("insert xxxxxxx",result,[](ec){});  
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-11-16 19:20:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/182#issuecomment-1815166809  

It depends on what you're trying to achieve:  
  
If what you mean is "after the first query succeeds, start the second one. When the second one finishes, start the third one", that's supported, but you need to tell it to your program. You can use something like:  
  
```  
void launch_query(tcp_connection& conn, int i = 0) {  
    conn.async_execute("insert ...", [&conn, i](error_code ec) {  
        if (ec)  
        {  
            // handle error  
        }  
          
        // call the function again, until i == 100  
        if (i < 100)  
            launch_query(conn, i + 1);  
    });  
}  
```  
  
If you can use stackful coroutines (C++11, using `boost::asio::spawn`) or stackless coroutines (C++20, using `boost::asio::co_spawn`), this gets much more natural:  
  
```  
void my_loop(tcp_connection& conn, boost::asio::yield_context yield)  
{  
    for (int i = 0; i < 100; ++i)  
        conn.async_execute("insert ...", yield);  
}  
  
// in main  
boost::asio::spawn(  
    ctx.get_executor(),  
    [&conn](boost::asio::yield_context yield) {  
        my_loop(conn, yield);  
    },  
    [](std::exception_ptr err) {  
        if (err) std::rethrow_exception(err);  
    }  
)  
```  
  
Here is [a complete example on coroutines](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/examples/async_coroutines.html).  
  
If what you mean is launching 100 queries in parallel, this can't be achieved using a single connection directly. The MySQL protocol is like HTTP1, request/reply. What you can do:  
  
1. You can open 100 connections and run each query in a connection. This can make sense if you then re-use the connections for anything else. I'm currently implementing #19 to make this easier. If you want to go this way, let me know and I'll write an example on how to do it.  
2. If you know what your queries look like ahead of time, you can use [semicolon-separated queries](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/multi_resultset.html#mysql.multi_resultset.multi_queries), compose a single, big query and execute it in a single go. Beware not to incur in SQL injection vulnerabilities when composing the query.  
3. If you want to be able to call `async_execute` while a query is in progress, you need an async locking mechanism, such that successive queries wait for the currently executing one to finish before proceeding. If this is what you want, let me know and I will write an example for you.  
  
Any other piece of information about you're trying to achieve may help me orient you.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: venjin  
> Created at: 2023-11-23 12:44:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/182#issuecomment-1824369681  

Ok，i see. Thank a lot.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-11-24 02:04:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/182#issuecomment-1825063626  

Great. If you have any other questions, please let me know.
