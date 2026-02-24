# #2791 - Multiple web socket client issues [Closed]

> Username: dinusha-a-insighture  
> Created at: 2024-01-06 18:17:53 UTC  
> Updated at: 2024-01-08 11:14:19 UTC  
> Closed at: 2024-01-08 11:14:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2791  

### Version of Beast  
  
Latest  
  
### Steps necessary to reproduce the problem  
  
Hi,  
  
I am creating 2 web socket clients connections  using boost beast lib to push 2 different messages to 2 different web socket servers. i was using below example from the documentation https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/quick_start/websocket_client.html  
  
Most of the things work fine. In my case one web socket client is writing text data, other one is writing binary data (with `ws.binary(true)`). I am running them in separate threads. how every when both runs at same time, i can see my binary data is corrupted in the middle. It works fine when i run them separately. In my use case i need to run both of them at the same time.  
  
Is there any work around for this?  
  
Thanks in advance!

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-07 07:47:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1879984070  

It sounds like a concurrency bug. It's hard to determine the issue without any sample code.  
  
Generally you don't need to use two threads; instead, utilize the async interface, and you can accommodate multiple clients in a single-threaded executor. This way, you won't have to deal with data-race issues.  
  
You can begin by extending this example: https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp

---

## Comment 2

> Username: dinusha-a-insighture  
> Created at: 2024-01-07 08:13:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1879989303  

Hi @ashtum Thanks for the quick reply. Appreciated that!  
  
Yes going to look at async examples as the next step. If you already aware some async example with multiple clients sending messages continuously instead a single send, please help point that too. That will be helpful too.  
  
Thanks again for your input. It was helpful!

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-07 08:43:10 UTC  
> Updated at: 2024-01-07 11:22:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1879994979  

Asynchronous operations don't block, allowing you to initiate multiple of them simultaneously. For example, duplicating this line will initiate two independent chains of operations for two clients:  
  
```C++  
std::make_shared<session>(ioc)->run(host, port, text);  
std::make_shared<session>(ioc)->run(host, port, text);  
```  
  
**Note:** you cannot initiate multiple asynchronous operations (such as async_read) on the same instance of a stream or socket. You must wait until the first one completes.  
  
You can learn a lot about asynchronous programming by reading this section in the Asio documentation: [Overview](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/overview.html).

---

## Comment 4

> Username: dinusha-a-insighture  
> Created at: 2024-01-07 10:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880020051  

Nice. Many thanks for the great info!

---

## Comment 5

> Username: dinusha-a-insighture  
> Created at: 2024-01-07 13:00:12 UTC  
> Updated at: 2024-01-07 13:12:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880053558  

hi @ashtum   
  
I have converted the program to use the async as recommended. But still i can see the same behaviour.  
  
I have 2 web socket clients. one is sending text data for status info and other one sending audio stream. This happens continuously after its started.  
  
```  
net::io_context ioc;  
  
std::make_shared<send_status_messages_session>(ioc)->run(status_messages_ws_server_host.c_str(), status_messages_ws_server_port.c_str());  
std::make_shared<send_audio_stream_session>(ioc)->run(audio_stream_ws_server_host.c_str(), audio_stream_ws_server_port.c_str());  
  
ioc.run();  
```  
  
below is how i am writing text data from my queue for text info as a continuous process  
```  
void on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)  
{  
	if (ec) return fail(ec, "connect");  
  
	// Turn off the timeout on the tcp_stream, because  
	// the websocket stream has its own timeout system.  
	beast::get_lowest_layer(ws_).expires_never();  
  
	// Set suggested timeout settings for the websocket  
	ws_.set_option(websocket::stream_base::timeout::suggested(beast::role_type::client));  
  
	// Set a decorator to change the User-Agent of the handshake  
	ws_.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {req.set(http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING) + " websocket-client-async"); }));  
  
	// Update the host_ string. This will provide the value of the  
	// Host HTTP header during the WebSocket handshake.  
	// See https://tools.ietf.org/html/rfc7230#section-5.4  
	host_ += ':' + std::to_string(ep.port());  
  
	// Perform the websocket handshake  
	ws_.async_handshake(host_, "/", beast::bind_front_handler(&send_status_messages_session::on_handshake, shared_from_this()));  
}  
  
void on_handshake(beast::error_code ec)  
{  
	if (ec) return fail(ec, "handshake");  
  
	// Send the message  
	if (!status_messages.empty()) {  
		message = status_messages.front();  
	}  
	ws_.async_write(net::buffer(message), beast::bind_front_handler(&send_status_messages_session::on_write, shared_from_this()));  
}  
  
void on_write(beast::error_code ec, std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec) return fail(ec, "write");  
  
	// Complete the message write and continue  
	if (message.size() > 0) {  
		status_messages.pop();  
		message = "";  
	}  
	on_handshake(ec);  
}  
```  
  
below is how i am writing binary data from my stream for audio info as a continuous process. by setting the `ws_.binary(true)` to enable binary in the websocket.  
```  
void on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)  
{  
	if (ec) return fail(ec, "connect");  
  
	// Turn off the timeout on the tcp_stream, because  
	// the websocket stream has its own timeout system.  
	beast::get_lowest_layer(ws_).expires_never();  
  
	// Set suggested timeout settings for the websocket  
	ws_.set_option(websocket::stream_base::timeout::suggested(beast::role_type::client));  
  
	// Set a decorator to change the User-Agent of the handshake  
	ws_.set_option(websocket::stream_base::decorator([](websocket::request_type& req) {req.set(http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING) + " websocket-client-async"); }));  
  
	// Update the host_ string. This will provide the value of the  
	// Host HTTP header during the WebSocket handshake.  
	// See https://tools.ietf.org/html/rfc7230#section-5.4  
	host_ += ':' + std::to_string(ep.port());  
  
	// Perform the websocket handshake  
	ws_.async_handshake(host_, "/", beast::bind_front_handler(&send_audio_stream_session::on_handshake, shared_from_this()));  
	ws_.binary(true);  
}  
  
void on_handshake(beast::error_code ec)  
{  
	if (ec) return fail(ec, "handshake");  
  
	// Send the message  
	auto data = audio_stream.data();  
	size = data.size();  
	ws_.async_write(data, beast::bind_front_handler(&send_audio_stream_session::on_write, shared_from_this()));  
}  
  
void on_write(beast::error_code ec, std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec) return fail(ec, "write");  
  
	// Complete the message write and continue  
	audio_stream.consume(size); // sent data is removed from input sequence  
	on_handshake(ec);  
}  
```  
  
When i write the stream from the other side which is web socket server, i see the stream corrupted. This is working 100% accurately when i run one web socket at a time. I also tried starting these 2 web socket clients in 2 different threads and also tried with 2 different ioc instances, but still its same.  
  
Appreciate any advice on this.  
  
Thanks in advance!

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-07 14:10:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880070656  

```C++  
	auto data = audio_stream.data();  
	size = data.size();  
	ws_.async_write(data, beast::bind_front_handler(&send_audio_stream_session::on_write, shared_from_this()));  
```  
`data` is located on the stack and will be destroyed before being used by `async_write`. It is important to note that `async_write` only initiates the write operation, and takes the buffer by reference. The actual write is scheduled and will occur at a later time.

---

## Comment 7

> Username: dinusha-a-insighture  
> Created at: 2024-01-07 16:04:12 UTC  
> Updated at: 2024-01-07 16:30:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880098988  

Hi @ashtum , Thanks a lot for the reply.  
  
Any idea what is the recommended way that i should use? That would be helpful.  
  
Thanks in advance!

---

## Comment 8

> Username: ashtum  
> Created at: 2024-01-07 17:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880116681  

You need to extend the lifespan of the `data` variable until the `async_write` operation completes. You can achieve this by either making `data` a member variable of your class or creating a shared pointer for `data` and passing it to `bind_front_handler` (note that `send_audio_stream_session::on_write` should also accept this shared pointer).

---

## Comment 9

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 06:48:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880461687  

Hi @ashtum   
  
Many thanks for the inputs.  
  
I tried below approach mentioned by passing it to the `on_write()` via `bind_front_handler `. But not sure how it will help as i can see the same behaviour.  
  
```  
	void on_handshake(beast::error_code ec)  
	{  
		if (ec) return fail(ec, "handshake");  
  
		// Send the message  
		auto data = audio_stream.data();  
		ws_.async_write(data, beast::bind_front_handler(&send_audio_stream_session::on_write, shared_from_this(), &data));  
	}  
  
	void on_write(boost::asio::const_buffers_1* data, beast::error_code ec, std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec) return fail(ec, "write");  
  
		// Complete the message write and continue  
		audio_stream.consume(bytes_transferred); // sent data is removed from input sequence  
		on_handshake(ec);  
	}  
```  
How does this help to expand the lifespan? May be i am missing more?  
  
On the other note, wondering why this is only happening when i use my 2 web socket clients? Stream is working fine when i only run my `send_audio_stream_session` socket client using `sync` and `async` options that i tried.  
  
Appreciated any advice on these two!  
  
Thanks in advance!

---

## Comment 10

> Username: ashtum  
> Created at: 2024-01-08 07:17:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880487692  

I expected a shared pointer of buffer but you are passing a raw pointer which doesn't extend the life of your buffer.  
could you show the implementation of audio_stream.data() what does it return? does it own the buffer?

---

## Comment 11

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 07:52:33 UTC  
> Updated at: 2024-01-08 07:53:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880519476  

Hi @ashtum   
  
Declaration is below in my `MainClass`  
```  
boost::asio::streambuf audio_stream;  
```  
  
That is getting passed to my `AudioClass`  
```  
new AudioClass(&audio_stream);  
```  
  
Then inside my `AudioClass` its getting this variable and start writing to that buffer upon audio receive which is coming from a 3rd party library.  
```  
boost::asio::streambuf* audio_stream;  
  
AudioClass(boost::asio::streambuf* audio_stream_param) {  
	audio_stream = audio_stream_param;  
  
};  
  
void AudioClass::onAudioRawDataReceived(AudioRawData* audioRawData) {  
	std::ostream os(audio_stream);  
	os.write((char*)audioRawData->GetBuffer(), audioRawData->GetBufferLen());  
}  
  
```  
  
Thanks!

---

## Comment 12

> Username: ashtum  
> Created at: 2024-01-08 07:59:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880526134  

Is `AudioClass::onAudioRawDataReceived` called from another thread? How can you ensure it is protected from being invoked while `async_write` is in progress?

---

## Comment 13

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 08:20:44 UTC  
> Updated at: 2024-01-08 08:22:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880551343  

Its coming from the 3rd party library as a callback. So far it works fine accurately when we using a single web socket client. Previously i was using sync method to keep the stream in proper order. This specific issue i raise, only happens when i use more than 1 web socket client.

---

## Comment 14

> Username: ashtum  
> Created at: 2024-01-08 08:26:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880559610  

How can you ensure `AudioClass::onAudioRawDataReceived` is protected from being invoked while `async_write` is in progress?

---

## Comment 15

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 08:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880577494  

Hi,  
  
What i am trying was to write the `AudioClass::onAudioRawDataReceived` to a buffer as it receives (without worrying about the write operation), while the other thread/process will read from that buffer and do the write operation and using the `async` or `async_write`.  
  
Thanks!

---

## Comment 16

> Username: ashtum  
> Created at: 2024-01-08 08:54:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880593636  

This is a data-race problem; you need to synchronize your access. Alternatively, you can use [experimental::basic_concurrent_channel](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__basic_concurrent_channel.html) and copy the audio samples into a container like `std::string` and send it to the channel. Inside the session class, utilize [async_receive](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/experimental__basic_concurrent_channel/async_receive.html) on the channel and extend the lifespan of the received buffer by moving it to a member variable such as `std::string buffer_;`. Finally, you can write that `buffer_` to the websocket.

---

## Comment 17

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 09:06:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880612080  

Will try those options to see whether it will help. Highly appreciated your detail replies so far to help on this matter. Many thanks for that.  
  
Also any idea why it ONLY happens during multiple web socket clients? It was working without any data-race issue on the audio stream when i use single web socket client which is dedicated for audio binary data transmission (that is without my 2nd web socket client who is responsible for sending text status data).

---

## Comment 18

> Username: dinusha-a-insighture  
> Created at: 2024-01-08 11:14:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2791#issuecomment-1880804986  

Hi @ashtum   
  
I was able to fix this now. data-race issue is corrected now and it was all good!  
  
Its due an internal variable issue.  
  
Thanks alot for the help!
