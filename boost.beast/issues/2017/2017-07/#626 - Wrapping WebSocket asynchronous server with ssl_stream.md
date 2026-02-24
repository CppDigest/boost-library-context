# #626 - Wrapping WebSocket asynchronous server with ssl_stream [Closed]

> Username: furchtlos  
> Created at: 2017-07-08 21:56:19 UTC  
> Updated at: 2017-07-08 23:59:41 UTC  
> Closed at: 2017-07-08 23:59:41 UTC  
> Url: https://github.com/boostorg/beast/issues/626  

Beast Version: 70  
Compiler: MSVC v141, compiled into an x86 binary  
  
This issue is really more of a help than anything, but I've attempted to wrap the Websocket Async example in the ssl_stream class provided by in the Server Framework example. Wrapping the stream type to `beast::websocket::stream<ssl_stream<boost::asio::ip::tcp::socket>>` instead of `beast::websocket::stream<boost::asio::ip::tcp::socket>` leads to only one line needing to be changed. `ws_.next_layer().close(ec);` to `ws_.next_layer().next_layer().close(ec);`  
  
On compiling, we receive this error:   
`C2664	'ssl_stream<boost::asio::ip::tcp::socket>::ssl_stream(const ssl_stream<boost::asio::ip::tcp::socket> &)': cannot convert argument 1 from 'boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::stream_socket_service<Protocol>>' to 'ssl_stream<boost::asio::ip::tcp::socket> &&'`  
  
I have no been able to track down exactly where this even is, but I have suspessions that it is in websocket::stream's constructor due to an instance of buffered_read_stream being initialized as `buffered_read_stream<NextLayer, multi_buffer> stream_;` If you can point me in the right direction I would be so grateful, I'm in love with this framework but it's a bit much for me to handle at the moment.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-08 22:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/626#issuecomment-313883545  

It looks like you're trying to copy the stream. Can you please paste the source code line which is generating the error? It is probably a constructor. You will need a two-argument constructor to construct a websocket stream which wraps an ssl_stream, probably `{ios, ctx}` where `ios` is the `io_service` and `ctx` is the `ssl::context`.

---

## Comment 2

> Username: furchtlos  
> Created at: 2017-07-08 23:59:35 UTC  
> Url: https://github.com/boostorg/beast/issues/626#issuecomment-313888248  

Wow I can't believe that was all that I needed! When constructing the Websocket stream (type: `beast::websocket::stream<ssl_stream<boost::asio::ip::tcp::socket>>`) I didn't pass along the ssl context.   
```/// Constructor  
connection(server& parent, boost::asio::ip::tcp::endpoint const& ep, boost::asio::ip::tcp::socket&& sock, boost::asio::ssl::context& ctx)  
	: log_(parent.log_)  
	, ep_(ep)  
	, ws_(std::move(sock), ctx)  
	, timer_(ws_.get_io_service(), (clock_type::time_point::max)())  
	, strand_(ws_.get_io_service())  
	, id_([]  
{  
	static std::atomic<std::size_t> n{ 0 };  
	return ++n;  
}())  
{  
	// Invoke the callback for new connections if set.  
	// This allows the settings on the websocket stream  
	// to be adjusted. For example to turn compression  
	// on or off or adjust the read and write buffer sizes.  
	//  
	if (parent.mod_)  
		parent.mod_(ws_);  
}```  
  
Thanks again for pointing me in the right direction, love the work you've done.
