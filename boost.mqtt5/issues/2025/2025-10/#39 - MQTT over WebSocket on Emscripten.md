# #39 - MQTT over WebSocket on Emscripten [Closed]

> Username: rafzi  
> Created at: 2025-10-22 13:30:17 UTC  
> Updated at: 2025-12-10 09:25:31 UTC  
> Closed at: 2025-12-10 09:25:31 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39  

By providing a class that quacks like a `beast::websocket::stream`, I was able to make Boost MQTT5 run in browsers via Emscripten. Since that was repeating stuff which is already part of Boost Beast and due to private members (see comment in `set_websocket_protocol`), I thought it would be nice if Boost Beast WebSocket Stream would already transparently work on Emscripten, so created this issue: https://github.com/boostorg/beast/issues/3033   
  
The question came up what the authors of Boost MQTT5 think? Do you think it is reasonable to create a transparent WebSocket Stream within Boost Beast - even though it would completely circumvent the TCP(+TLS)+HTTP stack?  
  
An alternative I could see on the Boost MQTT5 side would be to bundle a StreamType concept which works for Emscripten. Speaking of the StreamType concept, its documentation was not fully clear to me: https://spacetime.mireo.com/async-mqtt5/async_mqtt5/ref/StreamType.html . I did not need to provide a `lowest_layer_type` member type or `lowest_layer` member function and it was still sufficient. Nothing can be inherited from `boost::asio::ip::tcp::socket` because there is no socket on Emscripten. Additionally, there were some other expected member functions which are not sufficiently documented by just requiring the `AsyncStream` concept: close, set_option, async_handshake, read_some, write_some, open, is_open, remote_endpoint and async_connect.  
  
@vinniefalco @ashtum

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-10-22 18:11:26 UTC  
> Updated at: 2025-10-22 18:11:41 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3433606067  

I don't understand what you are asking for. You want a websocket stream that does not perform the websocket HTTP/1 Upgrade handshake? Can you please link your code?

---

## Comment 2

> Username: rafzi  
> Created at: 2025-10-23 07:13:19 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3435479587  

> I don't understand what you are asking for. You want a websocket stream that does not perform the websocket HTTP/1 Upgrade handshake? Can you please link your code?  
  
Yes, exactly. In browser environments, it is not possible to just open arbitrary sockets, so we have to directly use the APIs for WebSockets. I provided a possible implementation of that in the original issue: https://github.com/boostorg/beast/issues/3033  
  
On the user-side, it just looks like this:  
  
```c++  
#if __EMSCRIPTEN__  
using MqttClient = boost::mqtt5::mqtt_client<emscripten_websocket_stream, std::monostate, boost::mqtt5::logger>;  
#else  
using MqttClient = boost::mqtt5::mqtt_client<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>, boost::asio::ssl::context, boost::mqtt5::logger>;  
#endif  
```  
  
With a bit of supporting glue:  
  
```c++  
/*  
* All rights reserved, with the intention to contribute to Boost  
*/  
#pragma once  
  
#include "impl/emscripten_websocket_stream.h"  
  
#include <boost/mqtt5/websocket.hpp>  
  
// This file integrates emscripten_websocket_stream with Boost.MQTT5.  
// Compare: <boost/mqtt5/websocket.hpp> which does the same for Boost.Beast's websocket stream.  
  
namespace boost::mqtt5  
{  
template <>  
struct ws_handshake_traits<emscripten_websocket_stream>  
{  
  
	template <typename CompletionToken>  
	static decltype(auto) async_handshake(emscripten_websocket_stream& stream,  
	                                      boost::mqtt5::authority_path ap, CompletionToken&& token)  
	{  
		using namespace boost::beast;  
  
		// Set suggested timeout settings for the websocket  
		stream.set_option(websocket::stream_base::timeout::suggested(role_type::client));  
  
		stream.binary(true);  
  
		// TODO: cannot do that because it is impossible to retrieve the websocket protocol back from the  
		//       decorator without sending HTTP requests, which we cannot do in the browser.  
		// Set a decorator to change the User-Agent of the handshake  
		//stream.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {  
		//	req.set(http::field::sec_websocket_protocol, "mqtt");  
		//	req.set(http::field::user_agent, "boost.mqtt");  
		//}));  
		stream.set_websocket_protocol("mqtt");  
  
		stream.async_handshake(ap.host + ':' + ap.port, ap.path, std::forward<CompletionToken>(token));  
	}  
};  
  
namespace detail  
{  
  
// in namespace boost::mqtt5::detail to enable ADL  
template <typename CompletionToken>  
auto async_shutdown(emscripten_websocket_stream& stream, CompletionToken&& token)  
{  
	return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code)>(  
	    [&stream](auto&& handler) {  
		    auto ex = boost::asio::get_associated_executor(handler, stream.get_executor());  
		    auto alloc = boost::asio::get_associated_allocator(handler);  
		    boost::system::error_code ec;  
		    stream.close(ec);  
		    boost::asio::dispatch(  
		        ex, boost::asio::bind_allocator(alloc, [h = std::move(handler), ec]() mutable { h(ec); }));  
	    },  
	    token);  
}  
  
}  // namespace detail  
}  // namespace boost::mqtt5  
```

---

## Comment 3

> Username: biljazovic  
> Created at: 2025-10-23 12:01:54 UTC  
> Updated at: 2025-10-23 12:51:06 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3436561495  

Really intrigued by the possibility of Boost.MQTT5 running in a browser!  
  
Regarding the `StreamType` requirements:  
* Functions `close`, `set_option`, `open`, `is_open`, `remote_endpoint` and `async_connect` are indirectly required because the lowest layer must inherit from `asio::ip::tcp::socket`, and those functions are invoked on the lowest layer.  
* `write_some` and `read_some` shouldn’t be required; could you please share the error message you get when you omit them?  
* We could consider introducing a customization point that would cover option setting, opening, connecting and handshaking. That would make it possible to use a StreamType that doesn’t model an Asio socket. It would take some design and implementation work, but it’s definitely an interesting direction to explore if there’s enough interest.  
  
A couple of points on the implementation:  
* ~You don’t actually need `async_handshake` and `ws_handshake_traits`; those can be incorporated into `async_connect`.~ I see now that you can't get to host/target from async_connect, never mind.  
* `async_shutdown` can be placed in the namespace of `emscripten_websocket_stream` instead of `boost::mqtt5::detail`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-10-24 22:58:23 UTC  
> Updated at: 2025-10-24 23:04:53 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3445165679  

I understand now and I see the problem. MQTT documents the requirements of the next layer as _AsyncStream_ yet the implementation requires more than that.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-10-24 23:02:48 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3445171821  

Related: https://github.com/boostorg/mqtt5/issues/40

---

## Comment 6

> Username: rafzi  
> Created at: 2025-10-27 16:01:28 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/39#issuecomment-3452074336  

> Regarding the `StreamType` requirements:  
>   
>     * Functions `close`, `set_option`, `open`, `is_open`, `remote_endpoint` and `async_connect` are indirectly required because the lowest layer must inherit from `asio::ip::tcp::socket`, and those functions are invoked on the lowest layer.  
  
Since in this case I cannot inherit from the Asio TCP socket, could the requirements be lowered to "must provide the same interface as Asio TCP socket?  
  
>     * `write_some` and `read_some` shouldn’t be required; could you please share the error message you get when you omit them?  
  
Thank you, I can confirm that `read_some` and `write_some` were not required after all.  
  
>     * We could consider introducing a customization point that would cover option setting, opening, connecting and handshaking. That would make it possible to use a StreamType that doesn’t model an Asio socket. It would take some design and implementation work, but it’s definitely an interesting direction to explore if there’s enough interest.  
  
Unless this would take the burden of Asio internals from the user-provided implementation, I honestly do not see too much value there. I also cannot see much other interest than this specific implementation. Asio + Beast should cover all the other use-cases.  
  
>     * `async_shutdown` can be placed in the namespace of `emscripten_websocket_stream` instead of `boost::mqtt5::detail`.  
  
I could move `async_shutdown` into the namespace of `emscripten_websocket_stream`, thanks.  
  
@vinniefalco I don't actually see any problem besides the slight documentation inaccuracies.
