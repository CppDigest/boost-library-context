# #222 - boost redis library not working with valkey server (redis fork) [Closed]

> Username: PristalMMangattu  
> Created at: 2024-11-21 07:02:36 UTC  
> Updated at: 2024-11-27 08:02:39 UTC  
> Closed at: 2024-11-27 08:02:38 UTC  
> Url: https://github.com/boostorg/redis/issues/222  

Hi, I have the following program using boost redis library with valkey,  
  
==============START===============================  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
#include <boost/redis/src.hpp>  
#include <boost/system/error_code.hpp>  
#include <iostream>  
#include <string>  
#include <memory>  
  
namespace net = boost::asio;  
using boost::redis::connection;  
using boost::redis::request;  
using boost::redis::response;  
  
int main() {  
    try {  
        net::io_context io_context;  
  
        // Create a Redis connection  
                auto redis_conn = std::make_shared<connection>(io_context);  
  
        // Create a config object  
        boost::redis::config cfg;  
        cfg.addr = {"127.0.0.1", "8767"};  
                cfg.username = "default";  
  
        // Connect to the Redis server  
        redis_conn->async_run(cfg, {boost::redis::logger::level::debug}, [redis_conn, &io_context](const boost::system::error_code& ec) {  
                        if (ec) {  
                std::cout << "Error connecting to Redis: " << ec.message() << std::endl;  
                return;  
            }  
  
                        std::cout << "Connected to the server\n";  
                        io_context.stop();  
        });  
  
                std::cout << "Running io_context...\n";  
        // Run the io_context to perform asynchronous operations  
        io_context.run();  
                std::cout << "io_context completed\n";  
    } catch (const std::exception& e) {  
        std::cout << "Error: " << e.what() << std::endl;  
        return 1;  
    }  
  
    return 0;  
}  
============END================================  
  
Issue : Even though when this program is run, I see the connection is getting accepted in valkey logs like below,  
  
=================START===========================  
1369804:M 20 Nov 2024 21:29:02.115 - DB 0: 3 keys (0 volatile) in 4 slots HT.  
1369804:M 20 Nov 2024 21:29:05.613 - Accepted 127.0.0.1:45132 <=== Connected here.  
1369804:M 20 Nov 2024 21:29:07.149 - DB 0: 3 keys (0 volatile) in 4 slots HT.  
=================END===========================  
  
I don't see  "Connected to the server\n" message getting printed, which means the completion token is not getting called.  
The following is redis logs,  
  
==================START==========================  
valkey_try$ ./server  
Running io_context...  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:8767  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:8767  
(Boost.Redis) writer-op: 85 bytes written.  
(Boost.Redis) reader-op: 171 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
==================END===========================  
  
I see valkey also supports resp3, will boost redis work with valkey ?  
Is there any issue with the code I am trying ?

---

## Comment 1

> Username: criatura2  
> Created at: 2024-11-21 09:06:39 UTC  
> Url: https://github.com/boostorg/redis/issues/222#issuecomment-2490448819  

The program looks ok, you don't see `"Connected to the server\n"` printed because `async_run` will can its callback only when it finishes running. You can try calling `async_exec` to see if you can send commands.

---

## Comment 2

> Username: PristalMMangattu  
> Created at: 2024-11-22 12:29:38 UTC  
> Url: https://github.com/boostorg/redis/issues/222#issuecomment-2493650324  

@criatura2  Thank you looking into the Issue. I have verified that, I could send a command using `async_exec`.  
I was under the impression that completion token would be executed on successful connection.  
  
I know this is not a platform for queries but since API level detailed documentation is missing in,  
[https://www.boost.org/doc/libs/1_84_0/libs/redis/doc/html/index.html](url)  
  
Following is what I understood, please let me know if I am wrong somewhere,  
  
1. completion token is only executed if the connection is terminated by the client itself using `redis_conn.cancel()`  
2. corollary is that, completion token for `async_exec` is required only for stopping the io_context(if required) or for cleanup purpose.

---

## Comment 3

> Username: PristalMMangattu  
> Created at: 2024-11-27 08:02:38 UTC  
> Url: https://github.com/boostorg/redis/issues/222#issuecomment-2503174083  

Closing this ticket as this was not an issue at all.
