# #2285 - Start sync connection then switch to async [Closed]

> Username: gitgitwhat  
> Created at: 2021-07-13 20:23:51 UTC  
> Updated at: 2022-09-24 05:11:23 UTC  
> Closed at: 2022-09-24 05:11:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2285  

I'm slowly getting my websocket client/server working but I've hit a wall.  
  
I have the client setup the connection to the server synchronously because I don't want to the rest of the program running until a connection is made and the server accepts the client registration.  Once it is connected and registered, I want to pass the connection context off to a thread so there can be async comms between the client and server.    
  
After the registration, I can't get the async piece to work.  Below is an example of what I'm trying to do.  After the new thread is spun up, I call `run()` in the `WSClientSession` class which calls `async_read.`  `on_read` is immediately called with zero bytes read and the following error:  
  
    The I/O operation has been aborted because of either a thread exit or an application request  
  
I then try to call `dowrite` which calls `async_write` but the same error occurs immediately.  
  
Finally, I call `run()` of my global `net::io_context` variable but that also just returns immediately.  
  
Why does `on_read` get called as soon as I make the `async_read` call?  And more importantly, how can I keep the thread running so there can be arbitrary back and forth comms between the client and server?  The examples, while useful, are lacking in that respect.  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/asio/strand.hpp>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
void fail(beast::error_code ec, char const* what) {  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
class WSClientSession : public std::enable_shared_from_this<WSClientSession> {  
	websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws_;  
	beast::flat_buffer buffer_;  
  
	public:  
	explicit  
		WSClientSession(net::io_context& ioc, ssl::context& ctx)  
		: ws_(net::make_strand(ioc), ctx) {  
	}  
  
	void run(void) {  
		ws_.async_read(buffer_, beast::bind_front_handler(&WSClientSession::on_read, shared_from_this()));  
	}  
  
	void doclose(void) {  
		ws_.async_close(websocket::close_code::normal, beast::bind_front_handler(&WSClientSession::on_close, shared_from_this()));  
	}  
  
	bool dowrite(char *js) {  
		std::string msg(js);  
  
		ws_.async_write(net::buffer(msg), beast::bind_front_handler(&WSClientSession::on_write, shared_from_this()));  
		return true;  
	}  
  
	void on_write(beast::error_code ec, std::size_t bytes_transferred) {  
		printf("on_write: %d\n", (int)bytes_transferred);  
  
		if (ec) {  
			return fail(ec, "write");  
		}  
	}  
  
	void on_read(beast::error_code ec, std::size_t bytes_transferred) {  
		printf("on_read: %d\n", (int)bytes_transferred);  
  
		if (ec) {  
			return fail(ec, "read");  
		}  
  
		if (bytes_transferred > 0) {  
			//Do stuff here...  
		}  
  
                //Issue another async_read to keep things going.  
		ws_.async_read(buffer_, beast::bind_front_handler(&WSClientSession::on_read, shared_from_this()));  
	}  
  
	void on_close(beast::error_code ec) {  
		if (ec) {  
			return fail(ec, "close");  
		}  
	}  
};  
  
//Global variables  
net::io_context gWsIOC;  
ssl::context gWsSslCtx{ ssl::context::tlsv12_client };  
std::shared_ptr<WSClientSession> gWsSess;  
  
//Worker thread  
LPVOID WsStartAsync(LPVOID ctx) {  
  	gWsSess->run();  
	gWsSess->dowrite((PCHAR)"HERE IS A TEST WRITE");  
  
	gWsIOC.run();  
	printf("IOC.run() is done\n");  
  
	return 0;  
}  
  
//For demo purposes...  
BOOL DoRegistration() {  
	return TRUE;  
}  
  
BOOL WsInitConnection(PCHAR server) {  
	BOOL ret = FALSE;  
	DWORD tId = 0;  
  
	if (server == NULL) {  
		return FALSE;  
	}  
  
	std::string host(server);  
	auto const port = "8000";  
  
	try {  
		tcp::resolver resolver{gWsIOC};  
		websocket::stream<beast::ssl_stream<tcp::socket>> ws{ gWsIOC, gWsSslCtx };  
		auto const results = resolver.resolve(host, port);  
  
		auto ep = net::connect(get_lowest_layer(ws), results);  
  
		if(! SSL_set_tlsext_host_name(ws.next_layer().native_handle(), host.c_str()))  
			throw beast::system_error(  
				beast::error_code(  
					static_cast<int>(::ERR_get_error()),  
					net::error::get_ssl_category()),  
				"Failed to set SNI Hostname");  
  
		host += ':' + std::to_string(ep.port());  
  
		ws.next_layer().handshake(ssl::stream_base::client);  
  
		ws.set_option(websocket::stream_base::decorator(  
			[](websocket::request_type& req)  
			{  
				req.set(http::field::user_agent,  
					std::string(BOOST_BEAST_VERSION_STRING) +  
						" websocket-client-coro");  
			}));  
  
		ws.handshake(host, "/");  
		beast::flat_buffer buffer;  
  
		if (DoRegistration()) {  
			gWsSess = std::make_shared<WSClientSession>(gWsIOC, gWsSslCtx);  
			CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)WsStartAsync, NULL, 0, &tId)  
			ret = TRUE;  
		}  
	} catch(std::exception const& e) {  
		std::cerr << "Error: " << e.what() << std::endl;  
		ret = FALSE;  
	}  
  
	return ret;  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-13 20:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879382209  

I don't think you're queuing your writes.  
  
You must not have any more than one asynchronous write in progress at any one time.  
You must not have any more than one asynchronous read in progress at any one time.  
You must not have any more than one asynchronous close in progress at any one time.  
  
This example may help:  
  
https://github.com/test-scenarios/boost_beast_websocket_echo/tree/master/pre-cxx20/chatterbox

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-13 20:53:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879395067  

You shouldn't mix sync and async on the same open socket. Pick one and use it. If you want your program to block until something takes place, use the `io_context` to coordinate that activity.

---

## Comment 3

> Username: gitgitwhat  
> Created at: 2021-07-13 21:10:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879405553  

@vinniefalco Do you say "shouldn't" because it's bad form or because it's not supported?  If it's the former, that doesn't concern me.  But if it fundamentally breaks what I'm trying to do than that's a different issue.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-07-13 21:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879420025  

The implementation does not guarantee that mixing sync and async will work. Don't do it.

---

## Comment 5

> Username: gitgitwhat  
> Created at: 2021-07-13 21:47:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879424721  

Thanks.  Good to know.  So for my original question, and in reading issue #1589, it seems that a single stream cannot have a pending async_read in progress when async_write is called.  Is that correct?  @madmongo1, in his response, says you can't have more than 1 of any type of async operation in progress so I'm a bit confused about what can actually be done.    
  
If I want an arbitrary bi-directional client/server flow where the server may send data to the client at some point in time not necessarily in response to a client operation and vice versa, is that possible on a single connection?

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-07-13 21:50:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879426443  

You may have one read and one write and one close in progress at the same time.  
  
but you may not initiate a read while a read is in progress, etc.  
  
think of “chains” of these operations as running in parallel. Each chain governs one of the types of operation: read, write, close.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-07-13 22:06:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-879433928  

Discussed here:  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 8

> Username: gitgitwhat  
> Created at: 2021-07-14 16:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-880020040  

Thanks to both of you and the great reference materials.  I've gotten my fully async client/server working but the last piece I need is how an arbitrary client thread can queue a message to send to the server.  
  
In another comment (https://github.com/boostorg/beast/issues/1381), Vinnie wrote the following code  
  
```  
    // Get on the strand if we aren't already on it  
    if(! strand_.running_in_this_thread())  
        return net::post(  
                strand_,  
                std::bind(  
                    &websocket_session::send  
                    this->shared_from_this(),  
                    message));  
```  
  
This is obviously a very simple question but how do you define `strand_`?  In a different message you wrote something similar to this:  
  
`if (!_ws.next_layer().get_executor().running_in_this_thread()) {`  
  
but for `running_in_this_thread` the websocket _ws needs to be defined like so.  
  
`websocket::stream<tcp::basic_socket<asio::strand<net::io_context::executor_type>>> _ws;`  
  
But that causes an error saying `boost::asio::ip::tcp` has no member `basic_socket`.    
  
So, how can I convert my `_ws` definition (`websocket::stream<beast::ssl_stream<beast::tcp_stream>> _ws;`) so that it works with `get_executor().running_in_this_thread()`?

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-08-03 10:02:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-891712706  

`tcp::basic_socket<asio::strand<net::io_context::executor_type>>`  
  
Is spelled:  
  
`asio::basic_socket<asio::ip::tcp, asio::strand<net::io_context::executor_type>>`

---

## Comment 10

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2285#issuecomment-1008220753  

This issue has been open for a while with no activity, has it been resolved?
