# #3 - Polling for incoming messages [Closed]

> Username: elia-sysdesign  
> Created at: 2023-12-30 16:54:02 UTC  
> Updated at: 2024-01-24 12:56:52 UTC  
> Closed at: 2024-01-24 12:56:52 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/3  

Hello,  
  
Sorry for the intrusion.  
  
Just wondering what would be the best way to implement a polling mechanism that would trigger a callback whenever a new message is received.  
So far I only found examples that would receive a single message and then disconnect from the broker.  
I need to implement an endpoint that would maintain a connection with the broker and that would permanently subscribe to a specific topic and keep receiving messages on said topic.  
  
Thanks in advance.  
Elia

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-01-02 08:32:37 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/3#issuecomment-1873733143  

Hello!  
  
Essentially, you want to set a new `mqtt_client::async_receive` callback after invoking the previous one. With callbacks, it would look something like this:  
```  
namespace asio = boost::asio;  
  
using stream_type = asio::ip::tcp::socket;  
using client_type = async_mqtt5::mqtt_client<stream_type>;  
  
void receive_message(client_type& client) {  
	client.async_receive([&client](  
		async_mqtt5::error_code ec, std::string topic,  
		std::string payload, async_mqtt5::publish_props  
	) {  
                // session and our subscriptions are lost  
		if (ec == async_mqtt5::client::error::session_expired) {  
			// resubscribe...  
		} else if (ec) {  
			// break...  
		} else {  
			// process message  
			receive_message(client);  
		}  
	});  
}  
  
int main(int argc, char* argv[]) {  
	asio::io_context ioc;  
  
        // using signals to exit  
	asio::signal_set signals(ioc, SIGINT, SIGTERM, SIGBREAK);  
	signals.async_wait(  
		[&ioc](boost::system::error_code ec, int sig) {  
			ioc.stop();  
		}  
	);  
  
	client_type client(ioc, "");  
  
        // configure the client, subscribe and check if the subscription is successfully established  
  
        receive_message(client);  
  
        ioc.run();  
}  
```  
  
However, if you are able to, I would recommend using coroutines for this case, as it leads to simpler and cleaner code. Our [receiver ](https://spacetime.mireo.com/async-mqtt5/async_mqtt5/examples/receiver.html) example would be slightly modified to achieve the desired behaviour.  
```  
#include <boost/asio/as_tuple.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/use_awaitable.hpp>  
  
#include <boost/asio/ip/tcp.hpp>  
  
#include <async_mqtt5.hpp>  
  
namespace asio = boost::asio;  
constexpr auto use_nothrow_awaitable = asio::as_tuple(asio::use_awaitable);  
  
asio::awaitable<void> client_receiver(asio::io_context& ioc) {  
	async_mqtt5::mqtt_client<asio::ip::tcp::socket> client(ioc, "");  
  
        // configure client  
	client.credentials("tester", "", "")  
		.brokers("mqtt-broker", 1883)  
		.run();  
  
       // subscribe and check if the subscription is successful  
	auto [ec, sub_codes, sub_props] = co_await client.async_subscribe(...);  
  
       // if we failed to subscribe, co_return here  
  
	for(;;) {  
		auto [rec, topic, payload, publish_props] = co_await client.async_receive(use_nothrow_awaitable);  
  
		if (rec == async_mqtt5::client::error::session_expired) {  
			// resubscribe  
		} else if (rec) {  
			break;  
		} else {  
			// process message  
		}  
	}  
  
	co_return;  
}  
  
int main() {  
	asio::io_context ioc;  
  
        // using signals to exit  
	asio::signal_set signals(ioc, SIGINT, SIGTERM);  
	signals.async_wait(  
		[&ioc](boost::system::error_code ec, int sig) {  
			ioc.stop();  
		}  
	);  
  
	co_spawn(ioc, client_receiver(ioc), asio::detached);  
  
	ioc.run();  
}  
  
```  
  
If you have any further questions, feel free to ask!  
  
I hope this helps,  
Korina
