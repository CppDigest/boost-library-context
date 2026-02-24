# #211 - Reconnect stops working if server is down [Closed]

> Username: zlausd  
> Created at: 2024-09-19 13:30:42 UTC  
> Updated at: 2025-06-10 10:46:41 UTC  
> Closed at: 2025-06-10 10:46:41 UTC  
> Url: https://github.com/boostorg/redis/issues/211  

My environment is Boost 1.85, GCC 14.2.1 (C++23) and Redis 7.2.5.  
  
Observing a small quirk and not sure if its an issue with my setup or something else.   
If I leave the Redis server shutdown and initiate a redis::connection with async_run, then it will only attempt to reconnect two times.   
  
After the second ' Connection refused', nothing else on the connection instance works (i.e. async_exec no longer runs or cancels).  
  
**Expected behavior when Redis is down:** Perpetual reconnect loop and requests should be failing since ```cancel_if_not_connected``` is explicitly set to true.  
  
Boost/Redis logs with added timestamps  
  
```  
2024-09-19 13:20:33.714400268 (Boost.Redis) check-health-op: timeout disabled.  
2024-09-19 13:20:33.714497273 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-09-19 13:20:33.714646366 (Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
2024-09-19 13:20:33.714686392 (Boost.Redis) hello-op: Operation canceled  
2024-09-19 13:20:33.714699958 (Boost.Redis) hello-op: error/canceled. Exiting ...  
2024-09-19 13:20:33.714713033 (Boost.Redis) runner-op: Connection refused (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
2024-09-19 13:20:33.714730756 (Boost.Redis) Connection lost: Connection refused  
2024-09-19 13:20:36.715120375 (Boost.Redis) check-health-op: timeout disabled.  
2024-09-19 13:20:36.715291159 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-09-19 13:20:36.715513632 (Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
//NO MORE OUTPUT FROM HERE ON OUT  
```  
  
Reproduction source code  
  
```  
#include <chrono>  
#include <memory>  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
#include <boost/redis/src.hpp>  
  
namespace asio = boost::asio;  
using namespace boost::redis;  
using asio::awaitable;  
using asio::detached;  
using asio::deferred;  
  
awaitable<void> co_main(config cfg) try  
{  
    auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
    conn->async_run(cfg, logger(logger::level::debug), detached);  
  
    asio::steady_timer timer{co_await asio::this_coro::executor};  
  
    //Perpetual 30 second PING-PONG (PONG response is ignored, not relevant to issue)  
    for(;;)  {  
        timer.expires_after(std::chrono::seconds(30));  
        co_await timer.async_wait(deferred);  
  
        try {  
            request req;  
            req.push("PING");  
            //Request is never cancelled, even though we are not connected since Redis server is down  
            req.get_config().cancel_if_not_connected = true;   
            co_await conn->async_exec(req, ignore, deferred);  
        } catch(std::exception& e){  
            std::cerr << "(co_main loop) " << e.what() << std::endl;  
        }  
  
    }  
}catch(std::exception& ex){  
    std::cerr << "(co_main) " << ex.what() << std::endl;  
}  
  
auto main(int argc, char * argv[]) -> int  
{  
    try {  
        config cfg;  
        cfg.health_check_interval = {}; //Disable PING  
        cfg.reconnect_wait_interval = std::chrono::seconds(3); //Increase retry time  
  
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
> Created at: 2024-09-19 14:30:54 UTC  
> Updated at: 2024-09-19 14:38:20 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2361164180  

Hm, if I remove the following from ```boost/redis/detail/connection_base.hpp```, then things work as I would expect.   
That is, the connection instance will try to perpetually reconnect and any issued async_exec requests fail as cancelled.   
  
Once Redis server is back up and running, everything works as expected.   
  
```  
            // Protects the code below from being called more than  
            // once, see https://github.com/boostorg/redis/issues/181  
            if (std::exchange(cancel_run_called_, true)) {  
               return;  
            }  
```  
  
EDIT:  
  
Another solution that seems to work is calling ```cancel_on_conn_lost();``` in the above if statement? Don't fully understand the context for which the above ```if``` statement was added or if what I am suggesting is a proper fix.

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-09-21 21:12:56 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2365322157  

Unfortunately I can't reproduce this issue, I get normal reconnection attempts  
  
```  
$ ./example/cpp20_intro  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 85 bytes written.  
(Boost.Redis) reader-op: 163 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) reader-op: EOF received. Exiting ...  
(Boost.Redis) writer-op: canceled (3). Exiting ...  
(Boost.Redis) run-op: End of file (reader), Success (writer)  
(Boost.Redis) ping_op: error/cancelled (2).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) check-health-op: canceled. Exiting ...  
(Boost.Redis) runner-op: End of file (async_run_all), Operation canceled (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost: End of file  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) check-health-op: canceled. Exiting ...  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Connection refused (async_run_all), Operation canceled (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
# Removed some output here  
  
(Boost.Redis) ping_op: error/cancelled (1).  
(Boost.Redis) check-timeout-op: error/canceled. Exiting ...  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) check-health-op: canceled. Exiting ...  
(Boost.Redis) hello-op: Operation canceled  
(Boost.Redis) hello-op: error/canceled. Exiting ...  
(Boost.Redis) runner-op: Connection refused (async_run_all), Operation canceled (async_health_check) Operation canceled (async_hello).  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 85 bytes written.  
(Boost.Redis) reader-op: 163 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
```

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-09-21 21:18:31 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2365323844  

How are you shutting down the redis server? Since you are disabling health-checks if the server does not send EOF downstream the connection will never notice that redis is down and might behave as if it were connected.

---

## Comment 4

> Username: zlausd  
> Created at: 2024-09-21 21:38:21 UTC  
> Updated at: 2024-09-21 21:42:09 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2365329388  

I stop it simply by running ```systemctl stop redis```.  
  
However, I observe the same behavior if I run the sample program and the redis server is never started, i.e.  _no_ redis server is running. Effectively the program is trying to connect to a inactive/unused port. Could you please also try that scenario on your end?  
  
  
- Can you maybe provide more details on your environment?  
   -  So far I've tested this on Ubuntu 24.04 LTS and Archlinux using multiple toolchains (GCC 13/14 and Clang 18.1) and I observe the same behavior every time.   
- Would a Dockerbuild file that cleanly replicates this behavior be helpful? If it would, I'll try to create it.

---

## Comment 5

> Username: mzimbres  
> Created at: 2024-09-22 08:59:33 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2366190844  

Hi,  
  
> I stop it simply by running systemctl stop redis.  
  
Ok, that is also what I am doing.  
  
> However, I observe the same behavior if I run the sample program and  
> the redis server is never started, i.e. no redis server is running.  
> Effectively the program is trying to connect to a inactive/unused  
> port. Could you please also try that scenario on your end?  
  
Nothing changed, the reconnect loop runs non-stop until I restart the server.  
  
> Can you maybe provide more details on your environment?  
  
Debian testing + gcc 14.2.0  
  
> Would a Dockerbuild file that cleanly replicates this behavior be helpful? If it would, I'll try to create it.  
  
That would be really helpful. Thanks.

---

## Comment 6

> Username: zlausd  
> Created at: 2024-09-23 07:40:32 UTC  
> Updated at: 2024-09-23 07:41:13 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2367443968  

Created a batteries-included Dockerfile (please see attachment [Dockerfile_redis_reproduction.txt](https://github.com/user-attachments/files/17094083/Dockerfile_redis_reproduction.txt)) that makes a fresh executable that replicates this behavior.  
  
- Builds system   
- Install latest tagged vcpkg  
- Create cpp source and CMakeLists file  
- Patch redis logger implementation to output timestamps  
- Make reproduction executable  
  
I built it as you normally would, e.g. tagged with redis_reproduction ```docker build -t redis_reproduction --file Dockerfile_redis_reproduction.txt  .```  and then ran it   
  
```  
/tmp> docker run -it  -u root:root redis_reproduction         //Run built container  
  
[root@74ae4eb7c587 /]# /opt/build/repro         //Run executable within container (with no redis server running)  
2024-09-23 07:36:00.710083691 (Boost.Redis) check-health-op: timeout disabled.  
2024-09-23 07:36:00.710276978 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-09-23 07:36:00.710443875 (Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
2024-09-23 07:36:00.710515581 (Boost.Redis) hello-op: Operation canceled  
2024-09-23 07:36:00.710533636 (Boost.Redis) hello-op: error/canceled. Exiting ...  
2024-09-23 07:36:00.710545518 (Boost.Redis) runner-op: Connection refused (async_run_all), Success (async_health_check) Operation canceled (async_hello).  
2024-09-23 07:36:00.710565105 (Boost.Redis) Connection lost: Connection refused  
2024-09-23 07:36:03.710915954 (Boost.Redis) check-health-op: timeout disabled.  
2024-09-23 07:36:03.711023157 (Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
2024-09-23 07:36:03.711170588 (Boost.Redis) run-all-op: connected to endpoint Connection refused  
//No more output  
```

---

## Comment 7

> Username: mzimbres  
> Created at: 2024-09-23 20:31:12 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2369319919  

Many thanks. I will try it when I get some free time, that might take a week in my current schedule.

---

## Comment 8

> Username: mzimbres  
> Created at: 2024-09-23 20:35:42 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2369329576  

```cpp  
        cfg.health_check_interval = {}; //Disable PING  
        cfg.reconnect_wait_interval = std::chrono::seconds(3); //Increase retry time  
```  
  
btw, what is the point of disabling health-checks but enabling reconnects? Both are coupled to each other, with disabled health-checks we can only detect clean disconnects initiated by the server. It will react to a server that freezes for example.

---

## Comment 9

> Username: zlausd  
> Created at: 2024-09-24 11:34:57 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2371010852  

Thanks, hope it is helpful and I didn't do something silly to cause this behavior.   
  
No real point to disabling the health checks to be honest, just wanted to disable the implicit health-check to keep the logger a little bit quitter. Once I observed that it does not matter, i.e. the behavior remained the same, I left it disabled to have as few moving parts as possible.

---

## Comment 10

> Username: mzimbres  
> Created at: 2024-10-06 19:39:03 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2395557930  

Short feedback: I tried to reproduce this many times but still could not do it. I couldn't build the docker file too.

---

## Comment 11

> Username: zlausd  
> Created at: 2024-10-07 12:50:52 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2396843210  

Thank you for trying to reproduce. I don't know how our environments differ that you do not observe this... At a bit of a loss, especially if you can't get the supplied Dockerfile to build.  
  
I just retried a full rebuild of the docker file I attached. It took around half an hour, but it built on the first try for me :\

---

## Comment 12

> Username: mzimbres  
> Created at: 2024-10-12 11:23:17 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2408528539  

I just managed to reproduce this problem on your container and will investigate it further.

---

## Comment 13

> Username: Alvin-He  
> Created at: 2024-10-13 21:35:42 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2409139960  

Having a similar issue, my environment is Ubuntu, Linux 6.8.0-45-generic x86_64, boost 1.85 with boost cobalt  
  
when my app code is started with out the Redis Server running. `redis::async_run` attempts 1 reconnect, fails, and everything returns to normal. After `reconnect_wait_interval` passes since the 1st attempt, a 2nd attempt happens, but after this attempt fails, the entire program hangs and have to be killed with SIGKILL.   
  
all `redis::request::config` have `cancel_if_not_connected` set to true. so my expected behavior was to repeatedly attempt reconnect while canceling all requests when disconnected from redis server.       
  
I tried @zlausd's solution and it did help resolve this.   
> Another solution that seems to work is calling `cancel_on_conn_lost();` in the above if statement? Don't fully understand the context for which the above `if` statement was added or if what I am suggesting is a proper fix.  
  
source code for my calls to boost::redis:  
https://github.com/Alvin-He/monovis2024/blob/multi_process/cpp/src/network/redis.cpp  
  
program log; health_check_id here is unnamed:   
```  
redis initiated  
async_exec failed!, #1  
PING:   
camera 0 initiated  
starting main program loop  
unnamedrun-all-op: resolve addresses 127.0.0.1:6379  
async_exec failed!, #2  
unnamedcheck-timeout-op: Response has no value. Exiting ...  
unnamedhello-op: Operation canceled  
unnamedhello-op: error/canceled. Exiting ...  
unnamedping_op: error/cancelled (1).  
unnamedcheck-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
async_exec failed!, #3  
unnamedrun-all-op: connected to endpoint Operation canceled  
  
unnamedrunner-op: Operation canceled (async_run_all), Pong timeout. (async_health_check) Operation canceled (async_hello).  
unnamedConnection lost: Operation canceled  
async_exec failed!, #4  
async_exec failed!, #5  
<a bunch of these where an async_exec call gets canceled>  
async_exec failed!, #29  
async_exec failed!, #30  
unnamedrun-all-op: resolve addresses 127.0.0.1:6379  
async_exec failed!, #31  
unnamedrun-all-op: connected to endpoint Connection refused  
  
unnamedcheck-timeout-op: Response has no value. Exiting ...  
unnamedping_op: error/cancelled (1).  
unnamedcheck-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
Killed // <-- manually killed with SIGKILL  
```

---

## Comment 14

> Username: mzimbres  
> Created at: 2024-10-19 12:03:05 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2423790345  

Hi @zlausd, this [PR](https://github.com/boostorg/redis/pull/217) has some improvements that I think will fix this problem. I couldn't test it in the container because it uses vcpkg to pull boost so I will think another way. If you have the time to test that branch please let me know if it fixes the problem for you.

---

## Comment 15

> Username: bruno-viva  
> Created at: 2024-10-25 20:47:42 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2438769635  

If you are using a local redis running on a container:  
  
```  
docker run --restart=unless-stopped -p 6379:6379 -p 8001:8001 --name=server_local_redis \  
  -v redis_data:/data \  
  -e REDIS_ARGS="--appendonly yes --save 60 1 --loglevel warning" \  
  -d redis/redis-stack:latest  
```  
  
I was able to reproduce like this:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
#include <boost/redis/connection.hpp>  
#include <boost/redis/src.hpp>  
#include <iostream>  
#include <semaphore>  
  
void Test(boost::redis::connection& connection,  
    boost::asio::io_context& io_context,  
    std::counting_semaphore<1>& semaphore,  
    int counter) {  
  auto request = std::make_shared<boost::redis::request>();  
  request->push("SET", "key", "value");  
  auto response = std::make_shared<boost::redis::response<std::string>>();  
  io_context.post([&connection, request, response, &semaphore, counter]() {  
    std::cout << "post " << counter << std::endl;  
    connection.async_exec(  
        *request, *response,  
        [request, response, &semaphore, counter]  
            (const boost::system::error_code& error, std::size_t) {  
          if (error) {  
            std::cout << "Failed to execute: " << error.to_string() << std::endl;  
          }  
          std::cout << "Done for " << counter << std::endl;  
          semaphore.release();  
        });  
  });  
}  
  
int main() {  
  boost::asio::io_context io_context{1};  
  std::thread thread;  
  
  // Set up.  
  boost::redis::connection connection(io_context);  
  boost::redis::config config;  
  connection.async_run(config, {boost::redis::logger::level::info},   
    [](const boost::system::error_code& error) {  
    if (error) {  
      std::cout << "Redis server setup error: " << error.to_string() << std::endl;  
    }  
  });  
  thread = std::thread([&io_context] {  
    std::cout << "RedisGlobalCache running" << std::endl;  
    io_context.run();  
    std::cout << "RedisGlobalCache finished" << std::endl;  
  });  
  
  std::vector<std::unique_ptr<std::counting_semaphore<1>>> semaphores;  
  for (int it = 0; it < 3; ++it) {  
    semaphores.push_back(std::make_unique<std::counting_semaphore<1>>(0));  
    Test(connection, io_context, *semaphores[it], it);  
  }  
  
  // Stop.  
  system("docker stop server_local_redis");  
  std::cout << "Stopping local redis server" << std::endl;  
  std::this_thread::sleep_for(std::chrono::seconds(10));  
  
  // Add more work.  
  std::counting_semaphore<1> last_semaphore(0);  
  Test(connection, io_context, last_semaphore, 10);  
  
  // Start.  
  system("docker start server_local_redis");  
  std::cout << "Starting local redis server" << std::endl;  
  std::this_thread::sleep_for(std::chrono::seconds(1));  
  
  for (auto& semaphore : semaphores) {  
    semaphore->acquire();  
  }  
  last_semaphore.acquire();  
  io_context.post([&connection] { connection.cancel(); });  
  thread.join();  
  
  std::cout << std::endl << std::endl;  
  return 0;  
}  
```  
  
Compiled with:  
  
`clang -std=c++17 redis.cpp -lc++ -stdlib=libc++ -fuse-ld=lld -lssl -lcrypto -L/usr/local/lib/ -static`  
  
Output was:  
  
```  
$ ./a.out   
RedisGlobalCache running  
post 0  
post 1  
post 2  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 229 bytes written.  
(Boost.Redis) reader-op: 984 bytes read.  
(Boost.Redis) hello-op: Success  
Done for 0  
Done for 1  
Done for 2  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) run-op: Success (reader), Success (writer)  
server_local_redis  
Stopping local redis server  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
(Boost.Redis) runner-op: Success (async_run_all), Pong timeout. (async_health_check) Success (async_hello).  
(Boost.Redis) Connection lost.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
post 10  
server_local_redis  
Starting local redis server  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Pong timeout. (async_check_timeout).  
```  
  
It hangs at the end as no reconnections are attempted again and there are still pending operations. Adding `request->get_config().cancel_if_not_connected = true;` did not solve the issue. Thank you

---

## Comment 16

> Username: bruno-viva  
> Created at: 2024-10-25 20:59:15 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2438815440  

> Hi [@zlausd](https://github.com/zlausd), this [PR](https://github.com/boostorg/redis/pull/217) has some improvements that I think will fix this problem. I couldn't test it in the container because it uses vcpkg to pull boost so I will think another way. If you have the time to test that branch please let me know if it fixes the problem for you.  
  
Using the `develop` branch, I get the following compilation error with my code above:  
  
```  
$ clang -std=c++17 redis.cpp -lc++ -stdlib=libc++ -fuse-ld=lld -lssl -lcrypto -L/usr/local/lib/ -static  
In file included from redis.cpp:2:  
In file included from /usr/local/include/boost/redis.hpp:12:  
In file included from /usr/local/include/boost/redis/connection.hpp:10:  
In file included from /usr/local/include/boost/redis/detail/connection_base.hpp:17:  
/usr/local/include/boost/redis/detail/runner.hpp:243:4: error: use of class template 'connector' requires template arguments; argument deduction not allowed in non-static class member  
  243 |    connector ctor_;  
      |    ^~~~~~~~~  
/usr/local/include/boost/redis/detail/connector.hpp:80:7: note: template is declared here  
   80 | class connector {  
      |       ^  
In file included from redis.cpp:2:  
In file included from /usr/local/include/boost/redis.hpp:12:  
/usr/local/include/boost/redis/connection.hpp:408:10: error: cannot deduce return type 'auto &' for function with no return statements  
  408 |    auto& next_layer() noexcept  
      |          ^  
/usr/local/include/boost/redis/connection.hpp:412:16: error: cannot deduce return type 'const auto &' for function with no return statements  
  412 |    auto const& next_layer() const noexcept  
      |                ^  
/usr/local/include/boost/redis/connection.hpp:429:16: error: cannot deduce return type 'const auto &' for function with no return statements  
  429 |    auto const& get_ssl_context() const noexcept  
      |                ^  
4 errors generated.  
```  
  
(I downloaded the include/boost and just copied it to my boost include folder)

---

## Comment 17

> Username: mzimbres  
> Created at: 2024-10-25 21:36:47 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2438915486  

```  
/usr/local/include/boost/redis/detail/runner.hpp:243:4: error: use of class template 'connector' requires template arguments; argument deduction not allowed in non-static class member  
  243 |    connector ctor_;  
      |    ^~~~~~~~~  
/usr/local/include/boost/redis/detail/connector.hpp:80:7: note: template is declared here  
   80 | class connector {  
```  
  
The connector class is not a template anymore, can you please check the connector.hpp file was replaced? See  
  
- https://github.com/boostorg/redis/blob/b8a52e5e61275bc7dbfa6379cd9adf1053525966/include/boost/redis/detail/runner.hpp#L243  
- https://github.com/boostorg/redis/blob/b8a52e5e61275bc7dbfa6379cd9adf1053525966/include/boost/redis/detail/connector.hpp#L53

---

## Comment 18

> Username: bruno-viva  
> Created at: 2024-10-25 21:45:09 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2438924215  

> ```  
> /usr/local/include/boost/redis/detail/runner.hpp:243:4: error: use of class template 'connector' requires template arguments; argument deduction not allowed in non-static class member  
>   243 |    connector ctor_;  
>       |    ^~~~~~~~~  
> /usr/local/include/boost/redis/detail/connector.hpp:80:7: note: template is declared here  
>    80 | class connector {  
> ```  
>   
> The connector class is not a template anymore, can you please check the connector.hpp file was replaced? See  
>   
> * [redis/include/boost/redis/detail/runner.hpp](https://github.com/boostorg/redis/blob/b8a52e5e61275bc7dbfa6379cd9adf1053525966/include/boost/redis/detail/runner.hpp#L243)  
>         
>         
>            Line 243  
>         in  
>         [b8a52e5](/boostorg/redis/commit/b8a52e5e61275bc7dbfa6379cd9adf1053525966)  
>         
>       
>       
>         
>     
>             
>               
>              connector ctor_;  
> * [redis/include/boost/redis/detail/connector.hpp](https://github.com/boostorg/redis/blob/b8a52e5e61275bc7dbfa6379cd9adf1053525966/include/boost/redis/detail/connector.hpp#L53)  
>         
>         
>            Line 53  
>         in  
>         [b8a52e5](/boostorg/redis/commit/b8a52e5e61275bc7dbfa6379cd9adf1053525966)  
>         
>       
>       
>         
>     
>             
>               
>              class connector {  
  
My bad! I copied it to the wrong location. It compiled now, and it works with my example!  
  
```  
$ ./a.out   
RedisGlobalCache running  
post 0  
post 1  
post 2  
Failed to execute: boost.redis:16  
Done for 0  
Failed to execute: boost.redis:16  
Done for 1  
Failed to execute: boost.redis:16  
Done for 2  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 130 bytes written.  
(Boost.Redis) reader-op: 969 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) reader-op: End of file  
(Boost.Redis) run-op: End of file (reader), Success (writer)  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) hello: Success (async_hello), Operation canceled (async_check_health) End of file (async_run_lean).  
(Boost.Redis) Connection lost: End of file  
server_local_redis  
Stopping local redis server  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint Connection refused  
  
(Boost.Redis) Connection lost: Connection refused  
post 10  
server_local_redis  
Starting local redis server  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) writer-op: 163 bytes written.  
(Boost.Redis) reader-op: 974 bytes read.  
(Boost.Redis) hello-op: Success  
Done for 10  
(Boost.Redis) check-health-op: Operation canceled (async_ping), Operation canceled (async_check_timeout).  
(Boost.Redis) reader-op: Operation canceled  
(Boost.Redis) hello: Success (async_hello), Operation canceled (async_check_health) Operation canceled (async_run_lean).  
(Boost.Redis) Connection lost: Operation canceled  
Redis server setup error: system:125  
RedisGlobalCache finished  
  
```

---

## Comment 19

> Username: bruno-viva  
> Created at: 2024-10-25 22:02:17 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2438942150  

Related question: I have set `request->get_config().cancel_if_not_connected = true;`. Shouldn't my `post 10` have been cancelled?

---

## Comment 20

> Username: mzimbres  
> Created at: 2024-11-02 10:17:28 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2452944896  

> Related question: I have set request->get_config().cancel_if_not_connected = true;. Shouldn't my post 10 have been cancelled?  
  
I am not exactly sure what the code is doing. cancel_if_not_connected is checked only once at the time async_exec is called. that means, if a disconnect occurs after that it won't cause the request to be canceled. If you have more questions about that I suggest opening another issue.

---

## Comment 21

> Username: bruno-viva  
> Created at: 2024-11-04 01:15:18 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2453673848  

> > Related question: I have set request->get_config().cancel_if_not_connected = true;. Shouldn't my post 10 have been cancelled?  
>   
> I am not exactly sure what the code is doing. cancel_if_not_connected is checked only once at the time async_exec is called. that means, if a disconnect occurs after that it won't cause the request to be canceled. If you have more questions about that I suggest opening another issue.  
  
Thanks for responding, I think I got the gist of it. To clarify: if the connection to the server goes down, all enqueued async_exec will get cancelled if `cancel_on_connection_lost` is set to true (and `cancel_if_unresponded` to true if sent already), right?

---

## Comment 22

> Username: mzimbres  
> Created at: 2025-02-20 11:32:30 UTC  
> Url: https://github.com/boostorg/redis/issues/211#issuecomment-2671232080  

> Thanks for responding, I think I got the gist of it. To clarify: if the connection to the server goes down, all enqueued async_exec will get cancelled if cancel_on_connection_lost is set to true (and cancel_if_unresponded to true if sent already), right?  
  
@bruno-viva Correct.  
  
@zlausd Can we close this ticket now?
