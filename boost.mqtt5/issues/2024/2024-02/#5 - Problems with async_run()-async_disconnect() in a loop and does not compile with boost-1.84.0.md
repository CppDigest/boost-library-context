# #5 - Problems with async_run()/async_disconnect()  in a loop and does not compile with boost-1.84.0 [Closed]

> Username: lproj  
> Created at: 2024-02-09 13:25:54 UTC  
> Updated at: 2024-02-14 14:30:29 UTC  
> Closed at: 2024-02-14 14:30:29 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/5  

Hi,  
  
I have two separated problems with this library at the moment.  
  
- The first problem is that the library is not compatible with boost v1.84.0 (although it is with boost v1.83.0).  
- The second problem is that I do not see what's wrong in the minimal example below, where the same client is reused in a `async_run()/async_disconnect()` loop. It gets stuck on `async_run()` at some point, e.g. after two or three retries. However, if the client is recreated each time, then there are no problems.  
  
Thanks for your help!  
  
```  
#include <async_mqtt5.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/use_awaitable.hpp>  
  
asio::awaitable<void> client_receiver(asio::io_context& ioc) {  
    async_mqtt5::mqtt_client<asio::ip::tcp::socket> client(ioc, "");  
  
    client.brokers("localhost", 1883);  
  
    for (int retry = 0; retry < 10; retry++) {  
        client.async_run(asio::detached);  
  
        async_mqtt5::subscribe_topic sub_topic = async_mqtt5::subscribe_topic{  
            "test/mqtt-test", async_mqtt5::subscribe_options{  
                                  async_mqtt5::qos_e::exactly_once, async_mqtt5::no_local_e::yes,  
                                  async_mqtt5::retain_as_published_e::dont, async_mqtt5::retain_handling_e::not_send}};  
  
        // Subscribe to a single Topic.  
        auto [sub_codes, sub_props] =  
            co_await client.async_subscribe(sub_topic, async_mqtt5::subscribe_props{}, asio::use_awaitable);  
  
        // Disconnect the Client.  
        co_await client.async_disconnect(async_mqtt5::disconnect_rc_e::normal_disconnection,  
                                         async_mqtt5::disconnect_props{}, asio::use_awaitable);  
    }  
  
    co_return;  
}  
  
int main() {  
    asio::io_context ioc;  
    co_spawn(ioc, client_receiver(ioc), asio::detached);  
    ioc.run();  
}  
```

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-02-12 14:19:38 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/5#issuecomment-1938767666  

Hello!  
  
Thank you very much for submitting this issue!  
  
We have looked into it and were able to reproduce the issues you have described:  
- The fix to make Async.MQTT5 buildable with Boost 1.84 will be pushed later today.  
- We have detected the issue that causes your code to be stuck in the sample provided. However, the fix is more complicated and will take a few days.  
  
Thank you for your patience!  
  
Cheers,  
Korina
