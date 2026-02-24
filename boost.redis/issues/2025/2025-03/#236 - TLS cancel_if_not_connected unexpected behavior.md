# #236 - TLS cancel_if_not_connected unexpected behavior [Closed]

> Username: rotrida  
> Created at: 2025-03-14 11:49:41 UTC  
> Updated at: 2025-11-22 19:22:57 UTC  
> Closed at: 2025-11-22 19:22:57 UTC  
> Url: https://github.com/boostorg/redis/issues/236  

Hi,  
  
I'm using Boost 1.87, Ubuntu 24.10, clang 18.1.8, Redis 7.0.15.  
  
Iḿ trying to identify if my connection to Redis was a success, and in order to do that I was running PING after call async_run. I noticed, though, two different behaviors, and I use or not use TLS. 127.0.0.5 is not a valid Redis server.  
  
Running  with TLS I got an error, cancel doesn't work and I see on terminal that connection and handshake process is still happening. In the end, I just would like to know if connection as a success or not:  
  
```  
config cfg;  
     
cfg.addr.host = "127.0.0.1";  
cfg.addr.port = "6379";  
cfg.username = "default";  
cfg.password = "mypsw";  
cfg.database_index = 5;  
cfg.use_ssl = true;  
  
boost::asio::ssl::context ssl_ctx{boost::asio::ssl::context::tlsv12_client};  
  
ssl_ctx.load_verify_file("/etc/certificates/redis_client/ca.pem");  
ssl_ctx.use_certificate_file( "/etc/certificates/redis_client/client.pem", boost::asio::ssl::context::file_format::pem);  
ssl_ctx.use_private_key_file( "/etc/certificates/redis_client/client.key", boost::asio::ssl::context::file_format::pem);  
  
request req;  
req.get_config().cancel_if_not_connected = true;  
req.push("PING", "Hello world");  
  
response<std::string> resp;  
  
asio::io_context ioc;  
connection conn{ioc, std::move(ssl_ctx)};  
  
conn.async_run(cfg, {}, [](boost::system::error_code const& ec)  
{  
    std::cout << "async_run: " << ec.message() << std::endl;  
});  
  
conn.async_exec(req, resp, [&](auto ec, auto) {  
    if (!ec)  
        std::cout << "PING: " << std::get<0>(resp).value() << std::endl;  
        std::cout << "CONNECTED" << std::endl;  
    else  
        std::cout << "Exec error: " << ec.message() << std::endl;  
    conn.cancel();  
});  
  
ioc.run();  
```  
  
Exec error: Not connected.  
(Boost.Redis) run-all-op: resolve addresses 127.0.0.1:6379  
(Boost.Redis) run-all-op: connected to endpoint 127.0.0.1:6379  
(Boost.Redis) Runner: SSL handshake Success  
(Boost.Redis) writer-op: 143 bytes written.  
(Boost.Redis) reader-op: 170 bytes read.  
(Boost.Redis) hello-op: Success  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes read.  
(Boost.Redis) writer-op: 32 bytes written.  
(Boost.Redis) reader-op: 18 bytes  
  
Thanks,  
  
Rodrigo.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-03-14 12:22:54 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2724523712  

Hi Rodrigo, I will look at this problem when I am back from vacation in three weeks. If you need support sooner I advise you to look for help in the channel #boost-asio and cpplang slack space.

---

## Comment 2

> Username: rotrida  
> Created at: 2025-03-14 14:57:23 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2724955678  

No worries Marcelo. Enjoy your holiday.  
  
On Fri, 14 Mar 2025 at 12:23, Marcelo ***@***.***> wrote:  
  
> Hi Rodrigo, I will look at this problem when I am back from vacation in  
> three weeks. If you need support sooner I advise you to look for help in  
> the channel #boost-asio and cpplang slack space.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/redis/issues/236#issuecomment-2724523712>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AKTWNTUW4OTAPDXUMHLZX4T2ULC3HAVCNFSM6AAAAABZAQM6RGVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDOMRUGUZDGNZRGI>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
> [image: mzimbres]*mzimbres* left a comment (boostorg/redis#236)  
> <https://github.com/boostorg/redis/issues/236#issuecomment-2724523712>  
>  
> Hi Rodrigo, I will look at this problem when I am back from vacation in  
> three weeks. If you need support sooner I advise you to look for help in  
> the channel #boost-asio and cpplang slack space.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/redis/issues/236#issuecomment-2724523712>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AKTWNTUW4OTAPDXUMHLZX4T2ULC3HAVCNFSM6AAAAABZAQM6RGVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDOMRUGUZDGNZRGI>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-05-04 15:52:56 UTC  
> Updated at: 2025-05-04 15:53:10 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2849287727  

Hi and sorry again for the delay. I believe the problem arises [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/connection.hpp#L137)  
  
```cpp  
if (info_->get_request().get_config().cancel_if_not_connected && !conn_->is_open()) {  
   ...  
   return self.complete(error::not_connected, 0);  
}  
```  
  
That means, although`cancel_if_not_connected` is set, `conn_->is_open()` evaluates to `true` and therefore `async_exec` does not complete. The reason for why `conn_->is_open()` returns `true` can be seen in the [implementation](https://github.com/boostorg/redis/blob/develop/include/boost/redis/connection.hpp#L1069)  
  
```cpp  
auto is_open() const noexcept { return stream_->next_layer().is_open(); }  
```  
  
that means, `is_open` translates into _the tcp socket is open_. This is by no means a good criteria to determine if the client is connected to Redis (in this cases it is open but did not get past the tls handshake step). I am well aware this is a problem but added this anyway after a user asked for it because it looks like a valid need.  
  
I believe the best solution here would be to set a timeout in the `PING` request using the `cancel_after` token, there is already an [issue](https://github.com/boostorg/redis/issues/226) open for that.  
  
I am finishing other stuff at the moment so I can't make promises about when I will implement this. But I agree it should be given some priority.  
  
FYI: @anarthal

---

## Comment 4

> Username: anarthal  
> Created at: 2025-05-05 08:55:22 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2850336933  

You definitely need an `is_connected` member in your connection state that gets set to `true` whenever a successful connection attempt is made. `is_open` is only TCP as you said.  
  
I think this is gonna get solved much easier with your sans-io implementation. If you want, you can share the PR you're working at and I may be able to continue your work during these days.

---

## Comment 5

> Username: mzimbres  
> Created at: 2025-05-05 19:57:59 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2852189571  

> You definitely need an is_connected member in your connection state that gets set to true whenever a successful connection attempt is made.  
  
I don't think it is that simple, to know it is connected you have to actually try a command. If we offer `is_connected` people will write code like this  
  
```cpp  
if (conn.is_connected) {  
    co_await conn.async_exec(req, resp);  
}  
```  
  
and be surprised this can get blocked is the middle of a reconnect/failover operation. I think timeout is the only reliable way.  
  
> I think this is gonna get solved much easier with your sans-io implementation. If you want, you can share the PR you're working at and I may be able to continue your work during these days.  
  
Will do it.

---

## Comment 6

> Username: anarthal  
> Created at: 2025-05-07 09:21:16 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-2857821493  

I meant an internal member. I don't expose it in mysql. I completely agree it's very prone to mis-use.

---

## Comment 7

> Username: mzimbres  
> Created at: 2025-11-19 10:08:45 UTC  
> Url: https://github.com/boostorg/redis/issues/236#issuecomment-3551874035  

I believe we can close this ticket now. The new release 1.90 delivers per-op cancellation that can be used to check with more reliability whether the connection is healthy https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/example/cpp20_timeouts.cpp#L43  
  
Also `cancel_if_not_connected` is now deprecated https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/request.hpp#L62-L72
