# #213 - Allow integrating third party logger [Closed]

> Username: mrbald  
> Created at: 2024-09-27 11:59:58 UTC  
> Updated at: 2025-06-23 10:07:22 UTC  
> Closed at: 2025-06-23 10:07:22 UTC  
> Url: https://github.com/boostorg/redis/issues/213  

For pretty much any production usage you'd want to redirect all logging into the application log file (not std::cerr).  
Would be great to have a way to integrate a third party logger, can be as simple as a <severity, message> callback.

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-09-29 21:13:28 UTC  
> Url: https://github.com/boostorg/redis/issues/213#issuecomment-2381604862  

Hi,  
  
the original plan was to allow users pass their custom loggers to [basic_connection::async_run](https://github.com/boostorg/redis/blob/9039c3cd90992272338e3f2fe555e52745eb6c31/include/boost/redis/connection.hpp#L155) where the logger interface follows the `redis::logger` plublic api. This API however is unpolished and is likely to change in the future. It is also highly inconvenient to use the `basic_connection` instead of `connection` because of compilation times.  At the moment however this is the only workaround.

---

## Comment 2

> Username: JackD111  
> Created at: 2025-05-13 17:47:10 UTC  
> Url: https://github.com/boostorg/redis/issues/213#issuecomment-2877452811  

Why not just make the existing methods in `boost::redis::logger` virtual and it will allow us to plugin our own logger implementation?

---

## Comment 3

> Username: anarthal  
> Created at: 2025-05-13 19:38:45 UTC  
> Url: https://github.com/boostorg/redis/issues/213#issuecomment-2877740373  

If I understood Marcelo correctly, the problem is that this implies committing to API stability regarding the Logger interface. Seeing the signature of the current methods, they seem too tied to the current implementation.  
  
@mzimbres what would you think of making the logger interface have a single signature for all events? Something like  
  
```cpp  
void on_event(event_type type, formattable value);  
```  
  
Where event_type is an enum type that might be extended as the implementation changes, and formattable is a type-erased reference to an undetermined value that can be formatted, similar to std::basic_format_arg (but C++17 and encompassing types like asio::ip::tcp::resolver::results_type).  
  
We can then make a type-erased logger as @JackD111 suggests, or by any other type erasing techniques.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-05-17 15:30:25 UTC  
> Url: https://github.com/boostorg/redis/issues/213#issuecomment-2888459347  

I think a single signature is better than what we have right now. But even event_type seems unnecessary and I would prefer to lock into [syslog levels](https://github.com/mzimbres/smms/blob/master/src/logger.hpp#L30-L39), be it an enum type of functions named after them. I would also like to explore the idea proposed by @klemens-morgenstern about passing the logger in the completion token so we don't have to pollute functions signature with logger parameters as we have now.
