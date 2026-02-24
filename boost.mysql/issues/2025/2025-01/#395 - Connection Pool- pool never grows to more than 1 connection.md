# #395 - Connection Pool: pool never grows to more than 1 connection [Closed]

> Username: bruno-viva  
> Created at: 2025-01-03 02:07:06 UTC  
> Updated at: 2025-01-06 16:14:33 UTC  
> Closed at: 2025-01-06 16:14:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/395  

Hi,  
  
I am having trouble using the connection pool. I am sure it must be a silly mistake on my part, so please bear with me. The pool setup starts with 1 connection, and has a maximum of 4. However, the pool isn't growing and always has at most 1 connection. The minimal example I came up with is:  
  
```cpp  
#include <boost/asio/io_context.hpp>  
#include <boost/mysql/connection_pool.hpp>  
#include <iostream>  
#include <thread>  
  
void GetConnection(boost::mysql::connection_pool& pool) {  
  pool.async_get_connection([](boost::mysql::error_code error, boost::mysql::pooled_connection connection) {  
    if (error) {  
      std::cout << error.what() << std::endl;  
      return;  
    }  
    std::cout << "Got connection" << std::endl;  
    boost::mysql::pooled_connection& connection_ref = connection;  
    auto results = std::make_unique<boost::mysql::results>();  
    auto& results_ref = *results;  
    connection_ref->async_execute("SELECT CONNECTION_ID(); SELECT SLEEP(1)",  
      results_ref,  
      [connection = std::move(connection), results = std::move(results)](boost::mysql::error_code error) {  
        if (error) {  
          std::cout << error.what() << std::endl;  
          return;  
        }  
        std::cout << results->at(0).rows().at(0).at(0).as_uint64() << std::endl;  
      }  
    );  
  });  
}  
  
int main() {  
  boost::mysql::pool_params params;  
  params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
  params.username = "root";  
  params.password = "root";  
  params.database = "test_0";  
  params.max_size = 4;  
  params.thread_safe = true;  
  params.multi_queries = true;  
    
  boost::asio::io_context io_context;  
  boost::mysql::connection_pool pool(io_context, std::move(params));  
  pool.async_run(boost::asio::detached);  
    
  GetConnection(pool);  
  GetConnection(pool);  
  GetConnection(pool);  
  GetConnection(pool);  
    
  io_context.run();  
}  
```  
  
Compiled with: `clang++ -std=gnu++17 -stdlib=libc++ test.cpp -lboost_thread -lboost_charconv -lssl -lcrypto`  
  
The result is:  
  
```  
$ ./a.out   
Got connection  
8009  
Got connection <1 sec delay>  
8009  
Got connection <1 sec delay>  
8009  
Got connection <1 sec delay>  
8009  
```  
  
And in the terminal it waits ~1 second between each 'Got connection', which indicates to me that the pool is reusing a single connection only.  
  
I also noticed that the pooled_connection `Move assignment` differs from the `Move constructor`, where the assignment will return the connection to the pool. Is that intended? Even if I wrap it so that I only use the constructor, it works the same:  
  
```cpp  
struct ConnWrapper {  
  ConnWrapper(boost::mysql::pooled_connection connection) : connection_(std::move(connection)) {}  
  boost::mysql::pooled_connection connection_;  
};  
  
void GetConnection(boost::mysql::connection_pool& pool) {  
  ...  
    auto wrapper = std::make_unique<ConnWrapper>(std::move(connection));  
    boost::mysql::pooled_connection& connection_ref = wrapper->connection_;  
    auto results = std::make_unique<boost::mysql::results>();  
    auto& results_ref = *results;  
    connection_ref->async_execute("SELECT CONNECTION_ID(); SELECT SLEEP(1)",  
      results_ref,  
      [wrapper = std::move(wrapper), results = std::move(results)](boost::mysql::error_code error) {  
        ...  
```  
  
Shouldn't the pool grow from 1 to 4 connections? Setting the initial_size to 4 works and gets me all the 4 connections:  
  
```  
params.initial_size = 4;  
...  
$ ./a.out   
Got connection  
Got connection  
Got connection  
Got connection  
8020  
8021  
8022  
8019  
```  
  
What am I missing? Thanks in advance!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-01-03 10:35:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/395#issuecomment-2569017312  

Hi, thanks for your detailed bug report. This should work as you describe it, indeed.  
  
Right now, to determine whether a new connection should be created or not, we use an algorithm like the following:  
* If there is an available connection, return it, and don't create anything new.  
* If the number of connections is equal to the max, don't create anything.  
* If there is a "pending" connection (one that has been created but it's still not ready, possibly because it's connecting), don't create anything new.  
  
The third point is the one creating the problem here. When the `async_get_connection` requests arrive, there is just one connection (the one requested by `initial_size == 1`), but it's pending, so no new connections are created. Updating your code to the following:  
  
```cpp  
#include <boost/mysql/connection_pool.hpp>  
#include <boost/mysql/results.hpp>  
  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/system/detail/error_code.hpp>  
  
#include <iostream>  
  
int counter = 4;  
  
void GetConnection(boost::mysql::connection_pool& pool)  
{  
    pool.async_get_connection([&](boost::mysql::error_code error,  
                                  boost::mysql::pooled_connection connection) {  
        if (error)  
        {  
            std::cout << error.what() << std::endl;  
            return;  
        }  
        if (--counter > 0)  
            GetConnection(pool);  
        std::cout << "Got connection" << std::endl;  
        boost::mysql::pooled_connection& connection_ref = connection;  
        auto results = std::make_unique<boost::mysql::results>();  
        auto& results_ref = *results;  
        connection_ref->async_execute(  
            "SELECT CONNECTION_ID(); SELECT SLEEP(1)",  
            results_ref,  
            [connection = std::move(connection),  
             results = std::move(results)](boost::mysql::error_code error) {  
                if (error)  
                {  
                    std::cout << error.what() << std::endl;  
                    return;  
                }  
                std::cout << results->at(0).rows().at(0).at(0).as_uint64() << std::endl;  
            }  
        );  
    });  
}  
  
int main()  
{  
    boost::mysql::pool_params params;  
    params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
    params.username = "root";  
    params.password = "";  
    params.database = "boost_mysql_examples";  
    params.max_size = 4;  
    params.thread_safe = true;  
    params.multi_queries = true;  
  
    boost::asio::io_context io_context;  
    boost::mysql::connection_pool pool(io_context, std::move(params));  
    pool.async_run(boost::asio::detached);  
    GetConnection(pool);  
  
    io_context.run();  
}  
```  
  
Produces the expected output.  
  
Point 3 was created to avoid spuriously creating new connections when there is no server connectivity. But the algorithm is currently too naive. I will update it to make the snippet you posted work as intended.  
  
The consequences of this issue depend a lot on your use case. I'd say your pool will end up scaling, but at a slower rate than you'd expect.  
  
Thanks again for reporting,  
Ruben.

---

## Comment 2

> Username: bruno-viva  
> Created at: 2025-01-03 16:35:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/395#issuecomment-2569510948  

Thank you @anarthal !   
  
Regarding the `move constructor` and `move assignment` of the connection_pool, is it expected that the assignment version will call `return_connection` but the move constructor won't?   
  
I was a bit surprised that their behavior differs. I would expect them to have the same behavior, that is, to not return the connection yet (since the connection object isn't done yet). I think about it as similarly to unique_ptr:  
  
```cpp  
void Fun(std::unique_ptr<Obj> o);  
  
auto x = std::make_unique<Obj>();  
std::unique_ptr<Obj> y = std::move(x); // 1) --> y now owns x, but x didn't change any state about the contained object  
Fun(std::move(y));  // 2) --> transfer ownership to the function, y didn't change any state about the contained object  
```  
  
I would expect that the pooled_connection move assignment behaves similarly to 1), which doesn't change the underlying connection state. So chaining async handlers like this will be problematic:  
  
```cpp  
connection->async_execute("START TRANSACTION; INSERT INTO...",  
  ...,  
  [connection = std::move(connection)](boost::mysql::error_code error) {  
    // connection was reset, and is returned to the pool, since the move assignment happened here.  
    connection->async_execute("SELECT ...",  
      ...,  
        [connection = std::move(connection)](boost::mysql::error_code error) {}  
    );      
  }  
);  
```  
  
And even if that is the expected behavior, it will be problematic that the pool may give this connection to another async_get_connection as well, right? But maybe I am misunderstanding something? Thanks again

---

## Comment 3

> Username: anarthal  
> Created at: 2025-01-03 18:07:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/395#issuecomment-2569625483  

The `return_connection` in `operator=` returns the underlying connection that the `pooled_connection` being assigned to holds, to avoid leaking connections. It won't modify the source of the assignment. The following holds:  
  
```cpp  
pooled_connection c1 = co_wait pool.async_get_connection(); // c1 points to a valid connection  
pooled_connection c2 = co_wait pool.async_get_connection(); // c2 points to a valid connection  
  
// The connection pointed to by c2 gets returned to the pool, since we're no longer using it.  
// The connection pointed to by c1 (which has been transferred to c2) is still valid and hasn't been returned  
c2 = std::move(c1);  
```  
  
The behavior should match what `std::unique_ptr` does:  
  
```cpp  
std::unique_ptr<int> p1 (new int(42));  
std::unique_ptr<int> p2 (new int(50));  
p2 = std::move(p1); // 50 gets deleted, 42 is still valid  
```  
  
Are you observing a behavior different to what I describe?

---

## Comment 4

> Username: bruno-viva  
> Created at: 2025-01-03 21:10:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/395#issuecomment-2569822425  

My bad! You are right, please ignore my comments!
