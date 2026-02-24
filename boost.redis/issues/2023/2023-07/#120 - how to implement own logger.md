# #120 - how to implement own logger [Closed]

> Username: davehorton  
> Created at: 2023-07-06 10:35:13 UTC  
> Updated at: 2023-07-06 20:33:12 UTC  
> Closed at: 2023-07-06 20:29:36 UTC  
> Url: https://github.com/boostorg/redis/issues/120  

I'd like to implement my own logging function but see no docs or examples.  Can someone provide a brief pointer?  I have tried creating my own subclass of the boost::redis:logger class and passing that to  conn.async_run but it is not working (still getting the  logging to stdout)

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-07-06 20:28:29 UTC  
> Url: https://github.com/boostorg/redis/issues/120#issuecomment-1624270052  

Hi, at the moment, custom loggers are only possible to use with the `basic_connection`, which is the generic version of the `connection` class. For example  
  
```cpp  
using connection = boost::redis::basic_connection<asio::any_io_executor>;  
```  
  
The Logger is a template parameter of `connection::async_run` and must provide the following interface  
  
```cpp  
struct logger {  
   void on_resolve(system::error_code const& ec, asio::ip::tcp::resolver::results_type const& res);  
   void on_connect(system::error_code const& ec, asio::ip::tcp::endpoint const& ep);  
   void on_ssl_handshake(system::error_code const& ec);  
   void on_connection_lost(system::error_code const& ec);  
   void on_write(system::error_code const& ec, std::string const& payload);  
   void on_hello(system::error_code const& ec, generic_response const& resp);  
   void set_prefix(std::string_view prefix)  
};  
```  
  
The object passed to `async_run` will be copied by the implementation, so it should be lightweight.  
  
If your intention is to do away with log messages, you can also set the log level to e.g. `logger::level::emerg`. In the future there will be a level to simply suppress messages.  
  
That said, the `basic_connection` takes considerably longer to compile than `connection`. Therefore, I am planning to add an `any_logger` in the future that type erases the logger. Feel free to open an issue if you need this feature.

---

## Comment 2

> Username: davehorton  
> Created at: 2023-07-06 20:29:35 UTC  
> Url: https://github.com/boostorg/redis/issues/120#issuecomment-1624271175  

Thanks very much for the info.  For now I will just suppress the logging I guess.

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-07-06 20:33:11 UTC  
> Url: https://github.com/boostorg/redis/issues/120#issuecomment-1624274802  

Note to self:  
  
1. Add the information above to the docs.  
2. Add a `logger::level::suppress` to disable messages completely.  
3. Set the default log level to suppress. Logging to `stdout` is not expected by users.
