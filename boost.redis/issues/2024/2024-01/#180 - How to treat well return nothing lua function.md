# #180 - How to treat well return nothing lua function ? [Closed]

> Username: redboltz  
> Created at: 2024-01-24 06:16:02 UTC  
> Updated at: 2024-01-24 08:00:05 UTC  
> Closed at: 2024-01-24 08:00:04 UTC  
> Url: https://github.com/boostorg/redis/issues/180  

# Problem  
  
Redis supports server side script that is written by lua.  
Some of script do something but return nothing.   
Here is `redis-cli` example that execute (eval) empty script.  
  
```  
> redis-cli  
  
127.0.0.1:6379> EVAL "" 0  
(nil)  
127.0.0.1:6379>  
```  
  
I think it does nothing but not error.   
It is similar to C++ function `void f() {}`.  
  
The important point is that script return nothing.  
  
I worte a code that does the same thing using Boost.Redis (1.84.0)  
  
```cpp  
#include <string>  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
namespace redis = boost::redis;  
  
int main() {  
    asio::io_context ioc;  
    redis::config cfg;  
  
    redis::request req;  
    redis::response<std::string> resp;  
  
    auto conn = std::make_shared<redis::connection>(ioc.get_executor());  
  
    conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
    req.push("EVAL", "", 0);  
    conn->async_exec(  
        req,  
        resp,  
        [&](boost::system::error_code const& ec, std::size_t size) {  
            std::cout << "ec:" << ec.message() << std::endl;  
            std::cout << "size:" << size << std::endl;  
            std::cout << "resp.has_value():" << std::get<0>(resp).has_value() << std::endl;  
            std::cout << "resp.has_error():" << std::get<0>(resp).has_error() << std::endl;  
            if (std::get<0>(resp).has_error()) {  
                std::cout << "resp.error().diagnostic:" << std::get<0>(resp).error().diagnostic << std::endl;  
            }  
            conn->cancel();  
        }  
    );  
    ioc.run();  
}  
```  
  
I got the following result:  
  
```  
ec:Success  
size:3  
resp.has_value():0  
resp.has_error():1  
resp.error().diagnostic:  
```  
  
I think that the script is not error but `resp.has_error()` return true.   
  
  
When I replace   
  
```cpp  
    req.push("EVAL", "", 0);  
```  
  
with  
  
```cpp  
    req.push("EVAL", "xxx", 0);  
```  
  
, invalid lua script, then I got the following result:  
  
```  
> redis-cli  
  
127.0.0.1:6379> EVAL "xxx" 0  
(error) ERR Error compiling script (new function): user_script:1: '=' expected near '<eof>'  
```  
  
C++ program:  
  
```  
ec:Success  
size:86  
resp.has_value():0  
resp.has_error():1  
resp.error().diagnostic:ERR Error compiling script (new function): user_script:1: '=' expected near '<eof>'  
```  
In this case, the error is expected result.  
  
# My observation  
  
I think that lua script does not have errors but return nothing seems treat invalidly in Boost.Redis.  
Or my response type `redis::response<std::string>` could be wrong. But I'm not sure what type is appropriate.

---

## Comment 1

> Username: criatura2  
> Created at: 2024-01-24 07:51:59 UTC  
> Url: https://github.com/boostorg/redis/issues/180#issuecomment-1907579794  

Hi @redboltz, refer to the [Null section](https://www.boost.org/doc/libs/1_84_0/libs/redis/doc/html/index.html#autotoc_md7) in the docs, namely, replace `response<std::string> foo;` with `response<std::optional<std::string>> foo;`

---

## Comment 2

> Username: redboltz  
> Created at: 2024-01-24 08:00:05 UTC  
> Url: https://github.com/boostorg/redis/issues/180#issuecomment-1907589806  

@criatura2 , thank you for the prompt response. It works fine!  
In my script case, the value of optional is not important.   
So I use the following typealias for the no-return value script execution.  
  
```cpp  
using nil_t = std::optional<int>; // int is an example type of small size and no include dependency  
```
