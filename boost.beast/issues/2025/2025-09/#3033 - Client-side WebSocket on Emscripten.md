# #3033 - Client-side WebSocket on Emscripten [Closed]

> Username: rafzi  
> Created at: 2025-09-25 12:41:39 UTC  
> Updated at: 2025-10-27 16:03:13 UTC  
> Closed at: 2025-10-27 16:01:08 UTC  
> Url: https://github.com/boostorg/beast/issues/3033  

While using [Boost MQTT5](https://github.com/boostorg/mqtt5) `mqtt_client`, it is possible to pass `beast::websocket::stream` as template parameter to make WebSocket connections to the broker. However, this does not work on the Emscripten platform because the stream implementation will try to establish a TCP connection with TLS handshake to make an HTTP request to then do a WebSocket upgrade. All this is not supported in a browser environment.  
  
The same issue would apply if I tried to compile https://github.com/boostorg/beast/blob/develop/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp with Emscripten and ran it in a browser.  
  
I built an alternative stream implementation to plug into `mqtt_client`, which then uses the Emscripten API to manage a WebSocket connection. It would be nice if this would be directly integrated into `beast::websocket::stream`, so that the same user code would work on any platform. That way I could compile the example above for Emscripten and it would work in the browser without any code change.  
  
Other people also seem interested:  
- https://groups.google.com/g/emscripten-discuss/c/KRggI4a8gXo  
- https://stackoverflow.com/questions/44582467/how-to-use-boost-library-with-emscripten  
- https://stackoverflow.com/questions/76305737/how-implement-websockets-using-boost-library-in-webassembly  
  
I was wondering whether this was something in the interest of Boost Beast? What could be the next steps?  
  
My code currently only covers what is required by `mqtt_client`, so will need additions and polishing from extensive testing.  
  
```c++  
/*  
* All rights reserved, with the intention to contribute to Boost  
*/  
#pragma once  
  
#include <boost/asio.hpp>  
#include <boost/asio/associated_allocator.hpp>  
#include <boost/asio/associated_executor.hpp>  
#include <boost/asio/bind_allocator.hpp>  
#include <boost/asio/buffer.hpp>  
#include <boost/asio/post.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <cstdint>  
#include <cstring>  
#include <deque>  
#include <functional>  
#include <mutex>  
#include <optional>  
#include <type_traits>  
#include <vector>  
  
#include <emscripten/emscripten.h>  
#include <emscripten/websocket.h>  
  
namespace Fuse::ControlDevices  
{  
  
/// Implements a stream socket on the Emscripten platform. Notably it does not do any networking because there are no native socket APIs available.  
class emscripten_stream_socket  
{  
public:  
	using executor_type = boost::asio::any_io_executor;  
	using endpoint_type = boost::asio::ip::tcp::endpoint;  
  
	explicit emscripten_stream_socket(executor_type ex)  
	    : ex_(ex)  
	{  
	}  
  
	executor_type get_executor() const noexcept { return ex_; }  
  
	template <typename SettableSocketOption>  
	BOOST_ASIO_SYNC_OP_VOID set_option(const SettableSocketOption& option, boost::system::error_code& ec)  
	{  
		// Ignore.  
		BOOST_ASIO_SYNC_OP_VOID_RETURN(ec);  
	}  
  
	void open(const boost::asio::ip::tcp& protocol, boost::system::error_code& ec)  
	{  
		// There is nothing to open, because there is no native socket API.  
		ec = {};  
		is_open_ = true;  
	}  
	void open(const boost::asio::ip::tcp& proto)  
	{  
		boost::system::error_code ec;  
		open(proto, ec);  
		if (ec) {  
			throw boost::system::system_error(ec);  
		}  
	}  
  
	bool is_open() const noexcept { return is_open_; }  
  
	void close(boost::system::error_code& ec)  
	{  
		remote_ep_.reset();  
		is_open_ = false;  
		ec = {};  
	}  
  
	endpoint_type remote_endpoint(boost::system::error_code& ec) const  
	{  
		// TODO: does mqtt need this distinction?  
		/*if (em_websocket_ < 0) {  
			ec = boost::asio::error::not_connected;  
			return {};  
		}*/  
		if (!remote_ep_) {  
			ec = boost::asio::error::not_connected;  
			return {};  
		}  
		ec = {};  
		return *remote_ep_;  
	}  
  
	template <class CompletionToken>  
	auto async_connect(const endpoint_type& endpoint, CompletionToken&& token)  
	{  
		// There is nothing to connect to, because there is no native socket API.  
		remote_ep_ = endpoint;  
		return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code)>(  
		    [this, endpoint](auto&& handler) { handler(boost::system::error_code{}); }, token);  
	}  
  
protected:  
	executor_type ex_;  
	std::optional<endpoint_type> remote_ep_;  
	bool is_open_ = false;  
};  
  
class emscripten_websocket_stream : public emscripten_stream_socket  
{  
public:  
	explicit emscripten_websocket_stream(executor_type ex)  
	    : emscripten_stream_socket(ex)  
	{  
	}  
  
	void close(boost::system::error_code& ec)  
	{  
		if (em_websocket_ >= 0) {  
			emscripten_websocket_close(em_websocket_, 1000, "normal close");  
			emscripten_websocket_delete(em_websocket_);  
			em_websocket_ = -1;  
		}  
		emscripten_stream_socket::close(ec);  
		std::optional<PendingRead> pr;  
		{  
			std::lock_guard lk(m_);  
			pr.swap(pending_read_);  
		}  
		if (pr && pr->deliver) {  
			pr->deliver(boost::asio::error::operation_aborted, 0);  
		}  
	}  
  
	using emscripten_stream_socket::set_option;  
  
	void set_option(boost::beast::websocket::stream_base::timeout const& opt)  
	{  
		// TODO: implement timeouts and cancelations on handshake, accept, and close  
	}  
	void set_websocket_protocol(std::string protocol)  
	{  
		// We cannot implement set_option(decorator), because the decorator type is accessible only by beast::websocket::stream.  
		websocket_protocol_ = std::move(protocol);  
	}  
  
	void binary(bool value) { is_binary_ = value; }  
  
	template <class CompletionToken>  
	auto async_handshake(std::string_view host, std::string_view target, CompletionToken&& token)  
	{  
		std::string url = is_https() ? "wss://" : "ws://";  
		url += host;  
		if (!target.empty()) {  
			url += "/" + std::string(target);  
		}  
		return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code)>(  
		    [this, url](auto&& handler) {  
			    auto ex = boost::asio::get_associated_executor(handler, ex_);  
			    auto alloc = boost::asio::get_associated_allocator(handler);  
			    connect_handler_.emplace([ex, alloc, h = std::move(handler)](boost::system::error_code ec) mutable {  
				    boost::asio::dispatch(  
				        ex, boost::asio::bind_allocator(alloc, [h = std::move(h), ec]() mutable { h(ec); }));  
			    });  
  
			    EmscriptenWebSocketCreateAttributes attr;  
			    emscripten_websocket_init_create_attributes(&attr);  
			    attr.url = url.c_str();  
			    attr.protocols = websocket_protocol_.c_str();  
			    attr.createOnMainThread = true;  // TODO: can this be false?  
  
			    EMSCRIPTEN_WEBSOCKET_T s = emscripten_websocket_new(&attr);  
			    if (s < 0) {  
				    complete_connect_(boost::system::error_code{static_cast<int>(s), boost::system::system_category()});  
				    return;  
			    }  
			    em_websocket_ = s;  
  
			    emscripten_websocket_set_onopen_callback(em_websocket_, this, &on_open_thunk);  
			    emscripten_websocket_set_onmessage_callback(em_websocket_, this, &on_message_thunk);  
			    emscripten_websocket_set_onerror_callback(em_websocket_, this, &on_error_thunk);  
			    emscripten_websocket_set_onclose_callback(em_websocket_, this, &on_close_thunk);  
		    },  
		    token);  
	}  
  
	template <class MutableBufferSequence, class CompletionToken>  
	auto async_read_some(const MutableBufferSequence& bufs, CompletionToken&& token)  
	{  
		return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code, std::size_t)>(  
		    [this, &bufs](auto&& handler) {  
			    auto ex = boost::asio::get_associated_executor(handler, ex_);  
			    auto alloc = boost::asio::get_associated_allocator(handler);  
			    std::unique_lock lk(m_);  
			    // Immediate fulfill if we already have data  
			    if (!rx_bytes_.empty()) {  
				    std::size_t n = copy_out_(bufs);  
				    lk.unlock();  
				    boost::asio::dispatch(ex, boost::asio::bind_allocator(  
				                                  alloc, [h = std::move(handler),  
				                                          n](boost::system::error_code ec = {}) mutable { h(ec, n); }));  
				    return;  
			    }  
  
			    // Otherwise, park a pending read op  
			    pending_read_.emplace();  
			    pending_read_->buffers.clear();  
			    for (auto it = boost::asio::buffer_sequence_begin(bufs); it != boost::asio::buffer_sequence_end(bufs);  
			         ++it) {  
				    pending_read_->buffers.push_back(boost::asio::mutable_buffer(it->data(), it->size()));  
			    }  
			    pending_read_->deliver =  
			        boost::asio::any_completion_handler<void(boost::system::error_code, std::size_t)>(  
			            [ex, alloc, h = std::move(handler)](boost::system::error_code ec, std::size_t n) mutable {  
				            boost::asio::dispatch(ex, boost::asio::bind_allocator(  
				                                          alloc, [h = std::move(h), ec, n]() mutable { h(ec, n); }));  
			            });  
		    },  
		    token);  
	}  
  
	template <class MutableBufferSequence>  
	std::size_t read_some(const MutableBufferSequence& bufs, boost::system::error_code& ec)  
	{  
		std::lock_guard lk(m_);  
		if (rx_bytes_.empty()) {  
			ec = boost::asio::error::would_block;  
			return 0;  
		}  
		ec = {};  
		return copy_out_(bufs);  
	}  
  
	template <class ConstBufferSequence, class CompletionToken>  
	auto async_write_some(const ConstBufferSequence& bufs, CompletionToken&& token)  
	{  
		return boost::asio::async_initiate<CompletionToken, void(boost::system::error_code, std::size_t)>(  
		    [this, &bufs](auto&& handler) {  
			    auto ex = boost::asio::get_associated_executor(handler, ex_);  
			    auto alloc = boost::asio::get_associated_allocator(handler);  
			    auto complete = [ex, alloc, h = std::move(handler)](boost::system::error_code ec,  
			                                                        std::size_t n) mutable {  
				    boost::asio::dispatch(  
				        ex, boost::asio::bind_allocator(alloc, [h = std::move(h), ec, n]() mutable { h(ec, n); }));  
			    };  
  
			    if (em_websocket_ < 0) {  
				    complete(boost::asio::error::not_connected, 0);  
				    return;  
			    }  
  
			    if (!is_binary_) {  
				    throw std::runtime_error("text websocket protocol not implemented");  
			    }  
  
			    std::size_t total = 0;  
			    std::vector<std::uint8_t> tmp;  
			    for (auto it = boost::asio::buffer_sequence_begin(bufs); it != boost::asio::buffer_sequence_end(bufs);  
			         ++it) {  
				    const auto* p = static_cast<const std::uint8_t*>(it->data());  
				    tmp.insert(tmp.end(), p, p + it->size());  
				    total += it->size();  
			    }  
  
			    EMSCRIPTEN_RESULT r =  
			        emscripten_websocket_send_binary(em_websocket_, tmp.data(), static_cast<uint32_t>(tmp.size()));  
  
			    if (r < 0) {  
				    complete(boost::system::error_code{static_cast<int>(r), boost::system::system_category()}, 0);  
			    } else {  
				    complete({}, total);  
			    }  
		    },  
		    token);  
	}  
  
	template <class ConstBufferSequence>  
	std::size_t write_some(const ConstBufferSequence& bufs, boost::system::error_code& ec)  
	{  
		if (em_websocket_ < 0) {  
			ec = boost::asio::error::not_connected;  
			return 0;  
		}  
		if (!is_binary_) {  
			throw std::runtime_error("text websocket protocol not implemented");  
		}  
		std::size_t total = 0;  
		std::vector<std::uint8_t> tmp;  
		for (auto it = boost::asio::buffer_sequence_begin(bufs); it != boost::asio::buffer_sequence_end(bufs); ++it) {  
			const auto* p = static_cast<const std::uint8_t*>(it->data());  
			tmp.insert(tmp.end(), p, p + it->size());  
			total += it->size();  
		}  
		EMSCRIPTEN_RESULT r =  
		    emscripten_websocket_send_binary(em_websocket_, tmp.data(), static_cast<uint32_t>(tmp.size()));  
		if (r < 0) {  
			ec = boost::system::error_code{static_cast<int>(r), boost::system::system_category()};  
			return 0;  
		}  
		ec = {};  
		return total;  
	}  
  
private:  
	template <class MutableBufferSequence>  
	std::size_t copy_out_(const MutableBufferSequence& bufs)  
	{  
		std::size_t copied = 0;  
		for (auto it = boost::asio::buffer_sequence_begin(bufs);  
		     it != boost::asio::buffer_sequence_end(bufs) && !rx_bytes_.empty(); ++it) {  
			auto dst = *it;  
			auto* out = static_cast<unsigned char*>(dst.data());  
			std::size_t n = std::min<std::size_t>(dst.size(), rx_bytes_.size());  
			for (std::size_t i = 0; i < n; ++i) {  
				out[i] = rx_bytes_.front();  
				rx_bytes_.pop_front();  
			}  
			copied += n;  
		}  
		return copied;  
	}  
  
	void complete_connect_(boost::system::error_code ec)  
	{  
		std::optional<boost::asio::any_completion_handler<void(boost::system::error_code)>> h;  
		h.swap(connect_handler_);  
		if (h) {  
			(*h)(ec);  
		}  
	}  
  
	static EM_BOOL on_open_thunk(int, const EmscriptenWebSocketOpenEvent*, void* ud)  
	{  
		auto self = static_cast<emscripten_websocket_stream*>(ud);  
		self->complete_connect_({});  
		return EM_TRUE;  
	}  
  
	static EM_BOOL on_message_thunk(int, const EmscriptenWebSocketMessageEvent* e, void* ud)  
	{  
		auto self = static_cast<emscripten_websocket_stream*>(ud);  
		std::optional<PendingRead> ready;  
		std::size_t delivered = 0;  
  
		{  
			std::lock_guard lk(self->m_);  
			const auto* p = static_cast<const unsigned char*>(e->data);  
			self->rx_bytes_.insert(self->rx_bytes_.end(), p, p + e->numBytes);  
  
			if (self->pending_read_) {  
				delivered = self->copy_out_(self->pending_read_->buffers);  
				ready = std::move(self->pending_read_);  
				self->pending_read_.reset();  
			}  
		}  
  
		if (ready && ready->deliver) {  
			ready->deliver(boost::system::error_code{}, delivered);  
		}  
  
		return EM_TRUE;  
	}  
  
	static EM_BOOL on_error_thunk(int, const EmscriptenWebSocketErrorEvent*, void* ud)  
	{  
		auto self = static_cast<emscripten_websocket_stream*>(ud);  
		// If still connecting, fail connect  
		self->complete_connect_(make_error_code(boost::system::errc::io_error));  
  
		// Surface as read error if someone waits  
		std::optional<PendingRead> pr;  
		{  
			std::lock_guard lk(self->m_);  
			pr.swap(self->pending_read_);  
		}  
		if (pr && pr->deliver) {  
			pr->deliver(make_error_code(boost::system::errc::io_error), 0);  
		}  
		return EM_TRUE;  
	}  
  
	static EM_BOOL on_close_thunk(int, const EmscriptenWebSocketCloseEvent*, void* ud)  
	{  
		auto self = static_cast<emscripten_websocket_stream*>(ud);  
		std::optional<PendingRead> pr;  
		{  
			std::lock_guard lk(self->m_);  
			pr.swap(self->pending_read_);  
		}  
		if (pr && pr->deliver) {  
			pr->deliver(boost::asio::error::eof, 0);  
		}  
		return EM_TRUE;  
	}  
  
	// Returns true iff we are on an HTTPS page  
	static bool is_https()  
	{  
		// clang-format off  
		int em_is_https = EM_ASM_INT({  
			if (typeof self !== 'undefined' && self.location && self.location.protocol) {  
				return self.location.protocol === 'https:' ? 1 : 0;  
			}  
			return 0;  
		});  
		// clang-format on  
		return em_is_https != 0;  
	}  
  
private:  
	struct PendingRead  
	{  
		std::vector<boost::asio::mutable_buffer> buffers;  
		boost::asio::any_completion_handler<void(boost::system::error_code, std::size_t)> deliver;  
	};  
  
	EMSCRIPTEN_WEBSOCKET_T em_websocket_ = -1;  
	std::string websocket_protocol_;  
	bool is_binary_ = false;  
	std::optional<boost::asio::any_completion_handler<void(boost::system::error_code)>> connect_handler_;  
  
	std::mutex m_;  
	std::deque<unsigned char> rx_bytes_;  
	std::optional<PendingRead> pending_read_;  
};  
  
}  // namespace Fuse::ControlDevices  
  
```  
  
MQTT5 does the following to set the WebSocket protocol, but I cannot reuse that since it is private. Instead I added `set_websocket_protocol` which would not be required in a merged `beast::websocket::stream` implementation.  
  
```c++  
stream.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {  
	req.set(http::field::sec_websocket_protocol, "mqtt");  
}));  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-09-25 17:43:20 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3335227730  

Impressive grasp of Asio idioms !!!

---

## Comment 2

> Username: rafzi  
> Created at: 2025-10-20 14:29:14 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3422329605  

Any opinions on the integration of such an approach into Boost Beast? For example, I could imagine adding a bunch of `#ifdef __EMSCRIPTEN__` blocks into the implementation of `beast::websocket::stream` with the code above. Would that be acceptable? Any other ideas?  
  
I just want to avoid spending effort on the details if you already know it would be rejected.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-10-20 15:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3422567713  

@rafzi this might not be within the scope of Boost.Beast, since Beast provides a websocket implementation on top of Asio, while your class attempts to mimic the `beast::websocket::stream` interface so it can work with Boost.Mqtt5. It might be worthwhile to open a similar issue in that project and see what the author thinks.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-10-21 02:38:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3424444522  

You want to establish a websocket connection with beast, but without the usual HTTP UPGRADE mechanism?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-10-23 13:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3437060786  

I am reluctant to add `#ifdef __EMSCRIPTEN__`  to Beast, and I still do not understand what is being asked for. If Emscripten doesn't allow sockets, then you can just provide your own stream to use with beast's websocket.

---

## Comment 6

> Username: rafzi  
> Created at: 2025-10-24 20:25:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3444792042  

The requested feature is basically to have a websocket library which transparently works in browsers as well. That is, extending the supported platforms of `beast::websocket::stream` to Emscripten. That in turn would allow any library and application built on top of Beast, to support those platforms as well. For example my application built on top of Boost MQTT5 would "just work" in browsers.  
  
It is not possible to provide an alternative stream implementation to beast's websocket because the only way to use WebSocket in the browser is the WebSocket API. It is very high level, so takes only the payload. None of the framing that beast's websocket does should be done.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2025-10-24 22:53:27 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3445159886  

This is still not being explained at a sufficiently technical level that I can figure out how to do it.  
  
> The requested feature is basically to have a websocket library which transparently works in browsers as well.  
  
This tells me the motivation and does not offer me any insights into _**how**_.  
  
> ...extending the supported platforms of beast::websocket::stream to Emscripten  
  
Beast supports any platform where Asio is available. This includes Windows, MacOS, Linux, and many other platforms. What is different about Emscripten? You say that sockets are not available. If that is the case then what would replace it? Beast requires Asio. Is Asio available on Emscripten? If yes then choose a suitable stream. If not, then Beast cannot work there.  
  
> It is not possible to provide an alternative stream implementation to beast's websocket  
  
What does this mean? Are you talking about the `NextLayer` parameter to the Beast websocket?  
  
> ...the only way to use WebSocket in the browser is the WebSocket API  
  
I don't understand what you are saying. Are you referring to the WebSocket API in JavaScript? Are you talking about this:  
  
```  
let wss = new WebSocket("wss://your-secure-server-address.com/path");  
```  
  
> ...It is very high level, so takes only the payload  
  
What does this mean? What is very high level? What is "only taking the payload?" Are you talking about the JavaScript implementation?  
  
> None of the framing that beast's websocket does should be done.  
  
Are you talking about the header which is part of each websocket frame? You want Beast to not produce valid WebSocket frames?  
  
I want to see if we can help you, but this is not being explained in a way where I can make any technical evaluation.

---

## Comment 8

> Username: rafzi  
> Created at: 2025-10-25 11:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3446567113  

Sure, let's go into more technical depth.  
  
Asio is available for Emscripten and works fine in browsers, but any time it interacts with POSIX socket API (e.g. `asio::ip::tcp::socket`), it will not work out of the box. Emscripten does actually have some experimental support for socket APIs, but those require additional proxy components on the client or server side and are not mature or efficient: https://emscripten.org/docs/porting/networking.html  
  
The main point is that there really is no replacement for any kind of communication stream that would carry higher level protocols. As you correctly noted, this means that currently nothing inside Beast can work. For example, an HTTP or WebSocket *server* would never be possible. However, since Beast provides, among other things, an interface for a WebSocket client, there is a chance to provide this even for browsers, because those have a direct interface for WebSockets as well (HTTP clients could be implemented with XMLHttpRequest, but that is another topic).  
  
This means, I propose two completely different implementations depending on whether we run natively, or in a browser environment:  
  
<img width="670" height="482" alt="Image" src="https://github.com/user-attachments/assets/f54edf59-4318-4612-a03a-3f9b0a3407d7" />  
  
Yes, "WebSocket API" refers to JavaScript, but Emscripten provides an abstraction on top of those, see link above and my code in the first post (`#include <emscripten/websocket.h>`).  
  
If the app connects to "wss://1.2.3.4:9000", Beast WebSocket stream would just forward to `emscripten_websocket_new` which forwards to JavaScript WebSocket API, which does all the low level connection setup with sockets and HTTP upgrade and TLS handshake. Nothing about this low level connection setup is available to the app, it is part of the browser engine.  
  
Same with data frames: if the app wants to send the payload "abc", all we can do in the browser is forward that exact payload to `emscripten_websocket_send_binary`, which forwards to JavaScript, which forwards to browser internals that add headers and send over the socket.

---

## Comment 9

> Username: sehe  
> Created at: 2025-10-25 13:44:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3446720314  

> The requested feature is basically to have a websocket library which transparently works in browsers as well.  
  
Excuse me for joining in. I think the goal here is not accurately framed. To me it looks like there is *no* need for a websocket library that works "in browsers as well". What you seem to have (partly) implemented is an extension for use with the MQTT library that allows you to use it with the browser's websocket facilities. None of it requires any Beast. It just /looks/ a bit like Beast if you squint a little because the expected interface of the MQTT customization point mirrors `beast::websocket::stream<>` interface. I'd say such a feature could be handy but doesn't belong in beast (in fact, it *replaces* it).   
  
In the bigger picture there may be value in an Boost.Emscripten library (patterned somewhere between Boost.WinAPI and Boost.Python) because this is surely not the only OS abstraction that is different when targeting a browser. But otherwise I'd offer it as a "contrib" extension to the MQTT devs.  
  
Hope my $0.02 from the outside helps clarifying the picture.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2025-10-26 02:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3447945932  

Yeah I finally understood what is being asked for. That is, for MQTT to more formally define its _Stream_ concept so that alternate implementations such as the Emscripten stream can work with it.

---

## Comment 11

> Username: rafzi  
> Created at: 2025-10-27 16:01:08 UTC  
> Updated at: 2025-10-27 16:03:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3033#issuecomment-3452072936  

@sehe Thanks for your input. I feel like "there is no need" is a bit strongly worded. It certainly would have made my effort easier if my code "just worked" when compiled with Emscripten. It would also make for some nice demos to run existing Beast WebSocket examples in the browser. But I totally see your point that these two implementation have no common ground besides adhering to the same interface.
