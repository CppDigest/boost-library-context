# #22 - How can I use a psk/hint authentication? [Closed]

> Username: 0xFEEDC0DE64  
> Created at: 2024-11-19 17:18:23 UTC  
> Updated at: 2025-03-25 12:26:57 UTC  
> Closed at: 2025-03-25 12:26:57 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22  

I need psk/hint authentication for the mqtt_client, because a mqtt broker won't let me connect otherwise.  
I am pretty new to this boost asio and tcp and ssl stuff, so bear with me.  
I setup a boost::asio::ssl::context and I use `SSL_CTX_set_psk_client_callback(context.native_handle(), ...` to register a callback that then should return the right hint and psk key.  
For some reason no topics I subscribe seem to be published. I think the connection does not work but I dont have any way of getting errors.  
How should I proceed here?  
  
Running a mqtt "reverse proxy" that opens up the encrypted ssl connection and handles psk authentication just so that async-mqtt5 can then connect to localhost (my reverse proxy) seems to be a bit overkill, right?  
  
Is async-mqtt5 or boost asio generally able to do psk authentication?

---

## Comment 1

> Username: ksimicevic  
> Created at: 2024-11-20 12:36:25 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2488474291  

Yes, Asio supports PSK authentication on the SSL layer.  
  
The callback you registered in `SSL_CTX_set_psk_client_callback` should set the identity and the PSK. You could potentially use the hint (which can be null) to determine which identity to use.  
  
Here is a simple example of the callback that sets some predetermined identity and PSK:  
```cpp  
unsigned int psk_client_callback(  
	SSL* ssl,  
	const char* hint,  
	char* identity,  
	unsigned int max_identity_len,  
	unsigned char* psk,  
	unsigned int max_psk_len  
) {  
	// TODO: make sure you comply with max_identity_len and max_psk_len allowed  
  
	// Set the identity here, for example it is "client1"  
	std::string client_identity = "client1";  
	std::strncpy(identity, client_identity.c_str(), max_identity_len);  
  
	// Set the PSK by converting it to binary  
	std::string psk_key = "ABCD1234";  
	auto psk_length = psk_key.size() / 2;  
  
	auto binary = hex_to_binary(psk_key);  
	std::memcpy(psk, binary.data(), binary.size());  
  
	// if successful return psk_length else 0  
	return psk_length;  
}  
```  
Then, register the callback with:  
```cpp  
	SSL_CTX_set_psk_client_callback(context.native_handle(), psk_client_callback);  
```  
  
We are developing a logging system to notify users of connection issues. But until then, I recommend debugging connection issues by printing `error_code` messages at the following positions:  
1. [connect_op.hpp:121](https://github.com/mireo/async-mqtt5/blob/edb94108b6efb02e63445234f87367c00d873b55/include/async_mqtt5/impl/connect_op.hpp#L121) Print `ec.message()` to verify that TCP connection was successfully established or to determine the reason why it failed.  
2. [connect_op.hpp:159](https://github.com/mireo/async-mqtt5/blob/edb94108b6efb02e63445234f87367c00d873b55/include/async_mqtt5/impl/connect_op.hpp#L159) Print `ec.message()` to verify that SSL handshake was successful or to determine the reason why it failed. This step is crucial to verify successful PSK authentication.  
3. [connect_op.hpp:346](https://github.com/mireo/async-mqtt5/blob/edb94108b6efb02e63445234f87367c00d873b55/include/async_mqtt5/impl/connect_op.hpp#L346) Print `rc->message()` to determine the success of MQTT handshake.  
  
Note that the `mqtt_client` will indefinitely try to connect to the Broker, even if it continuously fails. This results in `async_publish`/`async_subscribe`/... call to never invoke their corresponding handler.

---

## Comment 2

> Username: 0xFEEDC0DE64  
> Created at: 2024-11-20 12:46:58 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2488496038  

Can I use async_run() to find out if the connection was established successfully? The examples show a boost::asio::detached, I guess I can hand over a callback lambda instead and get the error code then?

---

## Comment 3

> Username: 0xFEEDC0DE64  
> Created at: 2024-11-20 12:50:17 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2488502413  

Or how can I call this connect_op? Is there a async_reconnect() too?

---

## Comment 4

> Username: ksimicevic  
> Created at: 2024-11-20 13:58:16 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2488656523  

`async_run` only completes when the `mqtt_client` is cancelled (either by calling `cancel` or `async_disconnect`). This means you won't receive a connect error code there.  
  
We do not expose any connect/reconnect functions in our public API. That is part of our client's core design. We wrote a complete chapter on this design choice, and if you are curious, you can read it [in our documentation](https://spacetime.mireo.com/async-mqtt5/async_mqtt5/auto_reconnect.html).  
  
However, if it is important for you to know that your client is having trouble connecting, you can write a loop that publishes an empty payload to some topic (we could call it _keep-alive-topic_) every `x` seconds. If that operation doesn't complete within `y` time, you can cancel the client and handle this accordingly. In this case, you must carefully choose `x` and `y` values so they are not too restrictive. If that is something that seems helpful to you, I could provide you with an example of how to achieve that.

---

## Comment 5

> Username: 0xFEEDC0DE64  
> Created at: 2024-11-20 17:19:39 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2489159452  

Can I at least get events whenever the connection drops? At the moment we show the user status "connected" although we dont even know if we are connected or not.  
Also in my situation it seems like it does not reconnect at all.

---

## Comment 6

> Username: ksimicevic  
> Created at: 2024-11-21 14:11:31 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/22#issuecomment-2491312637  

Not at the moment. However, we are in the early stages of developing a similar concept that will notify you when the connection is lost or established and of other connectivity events.  
  
Regarding your reconnection problem, did you try debugging the lines in `connect_op.hpp` that I mentioned above?
