# #148 - MGET with invalid key unexpectedly reports error [Closed]

> Username: mrichmon  
> Created at: 2023-08-28 21:17:51 UTC  
> Updated at: 2023-08-31 16:34:33 UTC  
> Closed at: 2023-08-31 10:39:42 UTC  
> Url: https://github.com/boostorg/redis/issues/148  

The redis MGET operation is intended to complete without error in cases where keys are requested that do not exist. The result value for the keys that do not exist is nil.  
  
Using `boost_redis` the behavior is different. In the attached code, for `RESULT5` MGET is called with key that does not exist. After the call, the result object reports an error and no value available.  
  
Using `std::vector<std::optional<std::string>>` as the response type for that command does not compile due to no matching function call existing for the data conversion.  
  
What is the recommended way to issue MGET operations where the keys may not exist?  
  
  
```  
#include <boost/redis/src.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/asio/detached.hpp>  
#include <iostream>  
#include <map>  
  
#include <json/json.h>  
  
#include <chrono>  
#include <optional>  
  
namespace net = boost::asio;  
using boost::redis::connection;  
using boost::redis::request;  
using boost::redis::response;  
using boost::redis::config;  
  
auto main(int argc, char * argv[]) -> int  
{  
    try {  
        config cfg;  
  
        if (argc == 4) {  
            cfg.addr.host = argv[1];  
            cfg.addr.port = argv[2];  
            cfg.username = "default";  
            cfg.password = argv[3];  
        }  
        else {  
            std::cout << "error: requires 3 args -- <host> <port> <password>" << std::endl;  
            exit(1);  
        }  
  
        request req;  
        req.push("HELLO", "3", "AUTH", cfg.username, cfg.password);  
        req.push("PING", "Hello world");  
        req.push("GET", "test_data:v1:{144136013827014668}");  
        req.push("GET", "doesnotexist");  
        req.push("MGET", "test_data:v1:{144136013827014668}",  
                         "test_data:v1:{144136013831209032}",  
                         "test_data:v1:{144136013831209498}",  
                         "test_data:v1:{144136013831209498}");  
        req.push("MGET", "test_data:v1:{144136013827014668}",  
                         "test_data:v1:{144136013831209032}",  
                         "test_data:v1:{144136013831209498}",  
                         "test_data:v1:{144136013831209498}",  
                         "doesnotexist");  
  
        response<boost::redis::ignore_t,         // HELLO  
                 std::string,                    // PING  
                 std::string,                    // GET  
                 std::optional<std::string>,     // GET missing key  
                 std::vector<std::string>,       // MGET all keys exist  
                 std::vector<std::string>        // MGET one key does not exist  
                 > resp;  
  
        net::io_context ioc;  
        connection conn{ioc};  
  
        conn.async_run(cfg, {}, net::detached);  
        std::cout << "conn will reconnect: " << (conn.will_reconnect() ? "T" : "F") << std::endl;  
  
        auto start = std::chrono::high_resolution_clock::now();  
        conn.async_exec(req, resp, [&](auto ec, auto) {  
            if (!ec) {  
                std::cout << "RESULT1: PING:            " << std::get<1>(resp).value() << std::endl;  
                std::cout << "RESULT2: GET:             " << std::get<2>(resp).value() << std::endl;  
  
                std::cout << "RESULT3: GET missing key: has_value() - " << (std::get<3>(resp).has_value() ? "t" : "f") << std::endl;  
                std::cout << "RESULT3: GET missing key: optional.has_value() - " << (std::get<3>(resp).value().has_value() ? "t" : "f") << std::endl;  
  
                std::cout << "RESULT4: MGET:            " << std::to_string(std::get<4>(resp).value().size()) << std::endl;  
  
                std::cout << "RESULT5: MGET has error:  " << (std::get<5>(resp).has_error() ? "T" : "F") << std::endl;  
                std::cout << "RESULT5: MGET has_value:  " << (std::get<5>(resp).has_value() ? "T" : "F") << std::endl;  
            }  
            conn.cancel();  
        });  
  
        ioc.run();  
  
        auto elapsed = std::chrono::high_resolution_clock::now() - start;  
        long long microseconds = std::chrono::duration_cast<std::chrono::microseconds>(elapsed).count();  
  
        std::cout << "time (microseconds): " << std::to_string(microseconds) << std::endl;  
  
    } catch (std::exception const& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return 1;  
    }  
}  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-29 09:50:47 UTC  
> Url: https://github.com/boostorg/redis/issues/148#issuecomment-1697120147  

Hi, Boost.Redis does not support `vector<optional<string>>` yet. The alternatives are  
  
1. `vector<node<string>>`: General  
2. `response<optional<string>>`: If the number of keys is small and known at compile time  
  
I provide an example below  
  
```cpp  
auto co_main(config cfg) -> net::awaitable<void>  
{  
   auto conn = std::make_shared<connection>(co_await net::this_coro::executor);  
   conn->async_run(cfg, {logger::level::emerg}, net::consign(net::detached, conn));  
  
   request req;  
   req.push("SET", "key1", "value1");  
   req.push("SET", "key2", "value2");  
   req.push("SET", "key3", "value3");  
   req.push("MGET", "key1", "non-existent", "key2", "key3"); // Read in a vector<node>  
   req.push("MGET", "key1", "non-existent", "key2", "key3"); // Read in an embedded response.  
  
   response<  
      std::string,  
      std::string,  
      std::string,  
      std::vector<basic_node<std::string>>,  
      response<  
         std::optional<std::string>,  
         std::optional<std::string>,  
         std::optional<std::string>,  
         std::optional<std::string>>  
   > resp;  
  
   // Executes the request.  
   co_await conn->async_exec(req, resp, net::deferred);  
   conn->cancel();  
  
   std::cout  
      << std::get<0>(resp).value() << "\n"  
      << std::get<1>(resp).value() << "\n"  
      << std::get<2>(resp).value() << "\n"  
      ;  
  
   std::cout << "----" << std::endl;  
   for (auto const& e: std::get<3>(resp).value())  
      std::cout << to_string(e.data_type) << ": " << e.value << std::endl;  
  
   std::cout << "----" << std::endl;  
   std::cout << std::get<0>(std::get<4>(resp).value()).value().value_or("Null") << "\n";  
   std::cout << std::get<1>(std::get<4>(resp).value()).value().value_or("Null") << "\n";  
   std::cout << std::get<2>(std::get<4>(resp).value()).value().value_or("Null") << "\n";  
   std::cout << std::get<3>(std::get<4>(resp).value()).value().value_or("Null") << "\n";  
}  
```  
whose output is  
  
```  
OK  
OK  
OK  
----  
array:   
blob_string: value1  
null:   
blob_string: value2  
blob_string: value3  
----  
value1  
Null  
value2  
value3  
```  
  
Is this suitable for you? We can also add support to `std::vector<optinal<string>>` but it will only cover the cases where all strings have values that are simple data types, e.g. non aggregate.  
  
By the way, since you seem to be doing benchmarks, you might want to test the develop branch where I recently merged a branch that increases performance significantly. Also in https://github.com/boostorg/redis/issues/144 I am underway with usage connection information that are also useful on benchmarks.

---

## Comment 2

> Username: mrichmon  
> Created at: 2023-08-29 14:28:55 UTC  
> Url: https://github.com/boostorg/redis/issues/148#issuecomment-1697559005  

Thank you for those details. That will help improve the generic approach that I've been exploring.  
  
For now, I think the generic approach will work for me. My workload only requires GET and MGET operations so the generic implementation is straightforward.  
  
Right now, I'm trying to understand how to work with the boost_redis library. You are correct that I am working towards some performance measurements. I am interested in characterizing the multithreaded vs multiprocess perf. This also requires me to sort out why my `boost::asio` library compied with `clang` does not have `coawait` support.

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-08-29 15:13:24 UTC  
> Url: https://github.com/boostorg/redis/issues/148#issuecomment-1697642062  

> I am interested in characterizing the multithreaded vs multiprocess perf.  
  
I believe you won't ever need multi-thread since a single connection should be able to saturate both the network card and the Redis server for example, these are some numbers from a benchmark where I only send pings to Redis  
  
   - Commands sent: 2.3M/s  
   - Bytes sent: 97G/s  
   - Responses received: 2.3M/s  
   - Pushes received: 0  
   - Response bytes received: 65G/s  
   - Push bytes received: 0  
  
In another test where I also publish and receive pushes I get  
  
   - Commands sent: 503k/s  
   - Bytes sent: 19.5G/s  
   - Responses received: 503k/s  
   - Pushes received: 390k/s  
   - Response bytes received: 8G/s  
   - Push bytes received: 16.7G/s.  
  
And there are multiple things I can do to improve this performance.  
  
> This also requires me to sort out why my boost::asio library compied  
> with clang does not have coawait support.  
  
Have a look at how I do in the CI https://github.com/boostorg/redis/blob/509635f2227ef6f3c299d9d765fe1c8f385391bb/.github/workflows/ci.yml#L107

---

## Comment 4

> Username: mrichmon  
> Created at: 2023-08-31 16:34:33 UTC  
> Url: https://github.com/boostorg/redis/issues/148#issuecomment-1701387077  

For any future reader, getting co_await support working with `clang` required `-std=c++20` on my system.  
  
Boost compile:  
```  
./bootstrap.sh  --with-toolset=clang --prefix=/opt/test-boost-1.83.0 --with-python=/usr/bin/python3  
./b2 -j16 cxxflags=-fPIC cxxflags=-std=c++20 stdlib=native threading=multi link=static variant=release debug-symbols=on pch=off --layout=system install  
```
