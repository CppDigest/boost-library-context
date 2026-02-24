# #27 - Last Will & Testament (async_mqtt5::will) not working / no effect [Closed]

> Username: martinmeeser  
> Created at: 2025-02-10 14:34:38 UTC  
> Updated at: 2025-02-19 09:15:39 UTC  
> Closed at: 2025-02-12 09:25:49 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27  

Hello there,  
thank you for your work with the library.  
  
It seems like there is an issue with the last will & testament - I can not see it working.  
  
I just used the example from the homepage and added the will. As stated in the inline docs, I do it before the async_run().  
  
  
```  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
#include <async_mqtt5/mqtt_client.hpp>  
#include <async_mqtt5/types.hpp>  
  
int main() {  
	boost::asio::io_context ioc;  
  
	async_mqtt5::mqtt_client<boost::asio::ip::tcp::socket> c(ioc);  
	async_mqtt5::will w = async_mqtt5::will("hallo", "Goodbye cruel world!");  
	c.will(std::move(w));  
  
	c.brokers("0.0.0.0", 1883)  
		.credentials("mm-test1")  
		.async_run(boost::asio::detached);  
  
	c.async_publish<async_mqtt5::qos_e::at_most_once>(  
		"test/mm", "Hello world7!",  
		async_mqtt5::retain_e::no, async_mqtt5::publish_props {},  
		[&c](async_mqtt5::error_code ec) {  
			std::cout << ec.message() << std::endl;  
  
			throw std::runtime_error("This is a test exception");  
  
			// c.async_disconnect(boost::asio::detached); // disconnect and close the Client  
		}  
	);  
	  
	ioc.run();  
}  
```  
  
Setup:  
- gcc (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0  
- C++ 17  
- boost V1.87  
- async-mqtt5-1.0.2  
- nanomq as broker (note: lwt working with qtMqtt)  
  
  
Observed Behavior  
there is no message after forced runtime error  
  
Desired Behavior  
lwt works as expected

---

## Comment 1

> Username: ksimicevic  
> Created at: 2025-02-11 10:53:27 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27#issuecomment-2650449602  

Hello, and thank you for reporting this issue!  
  
The Client side of implementing LWT only involves sending the contents and properties of the Will message in the CONNECT packet to the Broker. The Broker decides when to publish the Will message.  
  
That said, I noticed that the Will message is not published when connecting to the NanoMQ broker.  I confirmed that LWT works as expected when connecting to the mosquito broker.  
  
The Broker is supposed to publish the Will message once the Client disconnects and its Session ends (as described [in the MQTT specification](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901040)). By default, the Session ends as soon as the Client disconnects (unless [Session Expiry Interval](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901048) is specified using `connect_property` function).  
  
It looks like NanoMQ handles Sessions differently from other Brokers. Running your example, I noticed that the NanoMQ broker kept the Session even after crashing the Client. As a result, the Will message was not published as expected.  
  
This can be fixed in one of the following ways:  
1) The Client specifies Session Expiry Interval using `connect_property`:  
```cpp  
c.connect_property(async_mqtt5::prop::session_expiry_interval, 1); // called before async_run, interval must be >=1, it does not work with 0  
```  
The Will message will be published sometime after `session_expiry_interval` passes.  
  
2)  The Client connects with `clean_session=1`. This is currently not customizable for our client, and we always connect with `clean_session=0`.  
  
I hope this helps.

---

## Comment 2

> Username: martinmeeser  
> Created at: 2025-02-12 08:49:43 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27#issuecomment-2653023669  

that worked, thank you very much!

---

## Comment 3

> Username: ksimicevic  
> Created at: 2025-02-12 09:25:49 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27#issuecomment-2653108046  

Glad to hear that!  
  
I'll close this issue now, but feel free to open new issues if you have any other questions or problems.

---

## Comment 4

> Username: JaylinYu  
> Created at: 2025-02-18 14:04:48 UTC  
> Updated at: 2025-02-18 14:05:34 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27#issuecomment-2665815076  

`we always connect with clean_session=0` this is the reason behind the issue.  
By MQTT Standard, as you said, only publish LWT when session ends, client with `clean_session=0` crashed doesnt represents the session is ended

---

## Comment 5

> Username: ksimicevic  
> Created at: 2025-02-19 09:15:36 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/27#issuecomment-2668008512  

> By MQTT Standard, as you said, only publish LWT when session ends, client with `clean_session=0` crashed doesnt represents the session is ended  
  
My mistake, I meant `clean_start=0`. We do not support MQTT v3.1 and the `clean_session` property.  
  
Regardless of the `clean_start` setting, the Session should expire when the connection is closed if the `session_expiry_interval` is 0 or absent as [specified](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901048):  
> If the Session Expiry Interval is absent the value 0 is used. If it is set to 0, or is absent, the Session ends  
when the Network Connection is closed  
  
 I suppose the source of confusion might be the non-normative comment in the MQTT5 specification which seems to be in contradiction with the previous statement:  
> Setting Clean Start to 1 and a Session Expiry Interval of 0, is equivalent to setting CleanSession  
to 1 in the MQTT Specification Version 3.1.1. **Setting Clean Start to 0 and no Session Expiry  
Interval, is equivalent to setting CleanSession to 0 in the MQTT Specification Version 3.1.1.**  
  
  
This specific issue was mentioned back in 2022 (https://github.com/mqtt/mqtt.org/issues/146).
