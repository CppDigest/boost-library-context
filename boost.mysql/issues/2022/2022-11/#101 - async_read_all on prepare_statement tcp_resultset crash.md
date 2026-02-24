# #101 - async_read_all on prepare_statement tcp_resultset crash [Closed]

> Username: NotDark  
> Created at: 2022-11-01 16:28:31 UTC  
> Updated at: 2022-11-01 22:57:27 UTC  
> Closed at: 2022-11-01 22:57:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/101  

```c++  
    auto user_getter =  connection.prepare_statement("select username from user where balance = ?");  
    user_getter.async_execute(boost::mysql::make_values(0), additional_info,  
                              [&](boost::mysql::error_code err, boost::mysql::tcp_resultset &&result)  
                              {  
                                  // auto rows=result.read_all(); // no crash  
                                  // std::cout << "size " << rows.size() << "\n";  
  
                                  // async_read_all crash  
                                  result.async_read_all(additional_info, [](boost::mysql::error_code err2, std::vector<boost::mysql::row> &rows)  
                                                           {  
                                                             std::cout << "size " <<rows.size() << "\n";  
                                                               
                                                           });  
                              });  
```  
  
but read_all no crash

---

## Comment 1

> Username: anarthal  
> Created at: 2022-11-01 22:57:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/101#issuecomment-1299328798  

You must keep the `resultset` returned by `async_execute` alive until `async_read_all` finishes. In your code, `result` goes out of scope after initiating `async_read_all`. On completion, `result` is accessed by the handler, but it has been destroyed, causing undefined behaviour.  
  
I suggest that you move `result` into a variable out of your lambda, that guarantees it will be valid until `async_read_all` finishes. Have a look at [this example](https://anarthal.github.io/mysql/mysql/examples/query_async_callbacks.html). It uses text queries instead of prepared statements, but it should give you some ideas on how to handle resultset lifetime correctly.  
  
Regards,  
Ruben.
