# #210 - Wrongly parsed redis::response when executing MULTI statement [Closed]

> Username: zlausd  
> Created at: 2024-09-17 16:30:25 UTC  
> Updated at: 2024-10-06 19:16:41 UTC  
> Closed at: 2024-10-06 19:16:40 UTC  
> Url: https://github.com/boostorg/redis/issues/210  

Let me preface the bug report that I may be doing something horribly wrong and misusing the library.   
  
Now that thats out of the way, lets start off at redis/redis-cli level and lower the description of the observation towards the output seen in C++ and at the very end is the code sample that replicates this behavior.   
  
Lets create a dummy test set  
  
`SADD TEST_SET a b c d e f g`  
  
**redis-cli output**  
```  
127.0.0.1:6379> SADD TEST_SET a b c d e f  
(integer) 6  
127.0.0.1:6379> SMEMBERS TEST_SET  
1~ "a"  
2~ "b"  
3~ "c"  
4~ "d"  
5~ "e"  
6~ "f"  
```  
  
And intersect it with a temporary set that contains the values a and z.  
  
```  
MULTI  
SADD TMP_SET a z  
SDIFF TMP_SET TEST_SET  
ECHO this_should_not_be_in_set  
DEL TMP_SET  
EXEC  
```  
**redis-cli output**  
```  
127.0.0.1:6379> MULTI  
OK  
127.0.0.1:6379(TX)> SADD TMP_SET a z  
QUEUED  
127.0.0.1:6379(TX)> SDIFF TMP_SET TEST_SET  
QUEUED  
127.0.0.1:6379(TX)> ECHO this_should_not_be_in_set  
QUEUED  
127.0.0.1:6379(TX)> DEL TMP_SET  
QUEUED  
127.0.0.1:6379(TX)> EXEC  
1) (integer) 2  
2) 1~ "z"                        <-- C++ string container should contain only one entry  
3) "this_should_not_be_in_set"  
4) (integer) 1  
```  
  
Everything looks fine and this works when translated into the same C++ code. The issue starts when the intersection is empty as the follow up command response is parsed to the member representing SDIFF.   
  
```  
MULTI  
SADD TMP_SET a  
SDIFF TMP_SET TEST_SET  
ECHO this_should_not_be_in_set  
DEL TMP_SET  
EXEC  
```  
**redis-cli output**  
```  
127.0.0.1:6379> MULTI  
OK  
127.0.0.1:6379(TX)> SADD TMP_SET a  
QUEUED  
127.0.0.1:6379(TX)> SDIFF TMP_SET TEST_SET  
QUEUED  
127.0.0.1:6379(TX)> ECHO this_should_not_be_in_set  
QUEUED  
127.0.0.1:6379(TX)> DEL TMP_SET  
QUEUED  
127.0.0.1:6379(TX)> EXEC  
1) (integer) 1  
2) (empty set)              <- Intersection is empty, therefore C++ string container should also be empty  
3) "this_should_not_be_in_set"   <-- But the following string is added into the container  
4) (integer) 1  
```  
  
  
The result of the SDIFF operator when I translate the above two MULTI transactions into boost/redis is as follows:  
  
```  
Not contained in TEST_SET: z   
Not contained in TEST_SET: this_should_not_be_in_set   
```  
  
Appreciate any feedback as to what I am doing wrong. Changing the type to either ```std::set<std::string>``` or ```std::optional<std::vector<std::string>>``` gives the same behavior.   
  
Code:  
  
```  
#include <boost/redis/connection.hpp>  
#include <boost/asio/deferred.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <vector>  
#include <set>  
#include <iostream>  
#include <ranges>  
#include <boost/redis/config.hpp>  
#include <boost/asio/use_awaitable.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
using boost::redis::request;  
using boost::redis::response;  
using boost::redis::ignore_t;  
using boost::redis::ignore;  
using boost::redis::config;  
using boost::redis::connection;  
using boost::asio::awaitable;  
using boost::asio::deferred;  
using boost::asio::detached;  
  
void print(std::map<std::string, std::string> const& cont)  
{  
   for (auto const& e: cont)  
      std::cout << e.first << ": " << e.second << "\n";  
}  
  
void print(std::ranges::range auto const & cont)  
{  
    for (auto const& e: cont) std::cout << e << " ";  
    std::cout << "\n";  
}  
  
auto populate_set(std::shared_ptr<connection> conn) -> awaitable<void>  
{  
   std::vector<std::string> test_data{"a","b","c","d","e","f"};  
   request req;  
   req.push_range("SADD", "TEST_SET", test_data);  
   
   co_await conn->async_exec(req, ignore, deferred);  
}  
  
auto test_set(std::shared_ptr<connection> conn, std::vector<std::string> query_data) -> awaitable<void>  
{  
    request req;  
    req.push("MULTI");  
    req.push_range("SADD", "TMP_SET", query_data);  
    req.push("SDIFF", "TMP_SET", "TEST_SET");  
    req.push("ECHO", "this_should_not_be_in_set");  
    req.push("DEL", "TMP_SET");  
    req.push("EXEC");  
  
    response<  
            ignore_t, // MULTI  
            ignore_t, // SADD  
            ignore_t, // SDIFF  
            ignore_t, // ECHO  
            ignore_t, // DEL  
            response<ignore_t,std::vector<std::string>,ignore_t, ignore_t> // EXEC  
    > resp;  
  
    co_await conn->async_exec(req, resp, deferred);  
  
    auto not_contained_values = std::get<1>(std::get<5>(resp).value()).value() | std::ranges::to<std::vector>();  
    std::cout << "Not contained in TEST_SET: ";  
    print(not_contained_values);  
}  
  
awaitable<void> co_main(config cfg)  
{  
    auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
    conn->async_run(cfg, {}, consign(detached, conn));  
  
    co_await populate_set(conn);  
    co_await test_set(conn, {"a", "z"}); //Works fine since intersection contains one element  
    co_await test_set(conn, {"a"}); //Skips empty set and adds the response to ECHO  
  
    conn->cancel();  
}  
  
auto main(int argc, char * argv[]) -> int  
{  
    try {  
        config cfg; //localhost instance, default/no account  
        asio::io_context ioc;  
        asio::co_spawn(ioc, co_main(cfg), [](std::exception_ptr p) {  
            if (p) std::rethrow_exception(p);  
        });  
        ioc.run();  
    } catch (std::exception const& e) {  
        std::cerr << "(main) " << e.what() << std::endl;  
        return 1;  
    }  
}  
```

---

## Comment 1

> Username: zlausd  
> Created at: 2024-09-17 17:34:13 UTC  
> Updated at: 2024-09-17 19:54:10 UTC  
> Url: https://github.com/boostorg/redis/issues/210#issuecomment-2356526544  

Modifying ```test_set``` to run commands as individual requests works as one would expect  
  
```  
Not contained in TEST_SET: z   
Not contained in TEST_SET:   
```  
  
  
```  
auto test_set(std::shared_ptr<connection> conn, std::vector<std::string> query_data) -> awaitable<void>  
{  
    {  
        request req;  
        req.push_range("SADD", "TMP_SET", query_data);  
        co_await conn->async_exec(req, ignore, deferred);  
    }  
    {  
        request req;  
        req.push("SDIFF", "TMP_SET", "TEST_SET");  
        response<std::vector<std::string>> resp;  
        co_await conn->async_exec(req, resp, deferred);  
        auto not_contained_values = std::get<0>(resp).value();  
        std::cout << "Not contained in TEST_SET: ";  
        print(not_contained_values);  
    }  
    {  
        request req;  
        req.push("ECHO", "this_should_not_be_in_set");  
        co_await conn->async_exec(req, ignore, deferred);  
    }  
    {  
        request req;  
        req.push("DEL", "TMP_SET");  
        co_await conn->async_exec(req, ignore, deferred);  
    }  
}  
```  
  
Edit: Not using MULTI/EXEC and putting everything into one request also seems to work fine.  
  
```  
auto test_intersection(std::shared_ptr<connection> conn, std::vector<std::string> query_data) -> awaitable<void>  
{  
    request req;  
    req.push_range("SADD", "TMP_SET", query_data);  
    req.push("SDIFF", "TMP_SET", "TEST_SET");  
    req.push("ECHO", "this_should_not_be_in_set");  
    req.push("DEL", "TMP_SET");  
  
  
    response<ignore_t, std::vector<std::string>, ignore_t, ignore_t > resp;  
    co_await conn->async_exec(req, resp, deferred);  
    auto not_contained_values = std::get<1>(resp).value();  
    std::cout << "Not contained in TEST_SET: ";  
    print(not_contained_values);  
}  
```

---

## Comment 2

> Username: criatura2  
> Created at: 2024-09-19 13:09:47 UTC  
> Url: https://github.com/boostorg/redis/issues/210#issuecomment-2360945235  

Hi, thanks for reporting. I will have a look at it on this weekend. For now you could check if the `generic_response` solves the problem for you.
