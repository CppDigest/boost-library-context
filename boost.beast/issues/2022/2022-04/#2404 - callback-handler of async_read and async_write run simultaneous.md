# #2404 - callback/handler of async_read and async_write run simultaneous [Closed]

> Username: chanfried  
> Created at: 2022-04-15 08:40:38 UTC  
> Updated at: 2023-04-02 19:46:37 UTC  
> Closed at: 2022-06-14 17:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2404  

### Version of Beast  
  
BOOST_BEAST_VERSION 330  
  
### Steps necessary to reproduce the problem  
When callback/handler of async_read and async_write run simultaneously, assert failed:  
  
"Assertion failed: ! *b_, file  **\boost\1_79_0\boost\beast\core\detail\stream_base.hpp, line 111"  
![image](https://user-images.githubusercontent.com/1514920/163546101-5fee92e3-49da-4779-87f4-ddf706d3d252.png)  
  
I understand that two of the same asynchronous I/O **_operation_** run at the same time is prohibit，but what about **_callback/handler_** of async_read and async_write run simultaneously?  
  
For ssl websocket: if construct  with strand(ws_(net::make_strand(ioc), ctx)), it works well; if construct without net::make_strand, assert fail.  
  
But for websocket without ssl,   construct with or without strand  is ok.  no assert fail

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-15 09:19:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1099989489  

It depends what you do in the callback handler.  
If you touch the stream, then you are introducing a race condition.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-15 14:20:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1100137406  

You need a strand.

---

## Comment 3

> Username: chanfried  
> Created at: 2022-04-15 16:23:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1100209631  

Thanks for your reply.  
  
Test code:  
  
  
```  
// Report a failure  
void fail(beast::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
	websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws_;  
	beast::flat_buffer read_buf_;  
	std::string write_buf_;  
	std::queue<std::string> queue_write_;  
	std::atomic_flag flag_sending_;  
	boost::mutex mutex_queue_;  
public:  
	// Take ownership of the socket  
	session(tcp::socket&& socket, ssl::context& ctx) : ws_(std::move(socket), ctx)  
	{  
		ws_.read_message_max(50L << 20); // 50M   
		flag_sending_.clear();  
	}  
  
	// Get on the correct executor  
	void run()  
	{  
		// We need to be executing within a strand to perform async operations  
		// on the I/O objects in this session. Although not strictly necessary  
		// for single-threaded contexts, this example code is written to be  
		// thread-safe by default.  
		net::dispatch(ws_.get_executor(),  
			beast::bind_front_handler(&session::on_run, shared_from_this()));  
	}  
  
	// Start the asynchronous operation  
	void on_run()  
	{  
		// Set the timeout.  
		beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
		// Perform the SSL handshake  
		ws_.next_layer().async_handshake(ssl::stream_base::server,  
			beast::bind_front_handler(&session::on_handshake, shared_from_this()));  
	}  
  
	void on_handshake(beast::error_code ec)  
	{  
		if (ec)  
			return fail(ec, "handshake");  
  
		// Turn off the timeout on the tcp_stream, because  
		// the websocket stream has its own timeout system.  
		beast::get_lowest_layer(ws_).expires_never();  
  
		// Set suggested timeout settings for the websocket  
		ws_.set_option(websocket::stream_base::timeout::suggested(beast::role_type::server));  
  
		// Set a decorator to change the Server of the handshake  
		ws_.set_option(websocket::stream_base::decorator([](websocket::response_type& res)  
			{res.set(http::field::server,std::string(BOOST_BEAST_VERSION_STRING));}));  
  
		// Accept the websocket handshake  
		ws_.async_accept(beast::bind_front_handler(&session::on_accept, shared_from_this()));  
	}  
  
	void on_accept(beast::error_code ec)  
	{  
		if (ec)  
			return fail(ec, "accept");  
  
		// Read a message  
		do_read();  
	}  
  
	void do_read()  
	{  
		// Read a message into our buffer  
		std::cout << "<<<  async read start...." << std::endl;  
		ws_.async_read(read_buf_, beast::bind_front_handler(&session::on_read, shared_from_this()));  
	}  
  
	void on_read( beast::error_code ec, std::size_t bytes_transferred)  
	{  
		std::cout << "<<<  async read complete, read sized:" << bytes_transferred << std::endl;  
		// This indicates that the session was closed  
		if (ec == websocket::error::closed)  
			return;  
  
		if (ec || !bytes_transferred)  
			return fail(ec, "read");  
		// Echo the message  
		{  
			boost::mutex::scoped_lock locker(mutex_queue_);  
			queue_write_.push(std::string(net::buffers_begin(read_buf_.data()), net::buffers_end(read_buf_.data())));  
		}  
		read_buf_.consume(read_buf_.size());  
		do_read();  
		if (!flag_sending_.test_and_set()) // no async write happening  
			do_write();  
		else  
			std::cout << "===  async write in process, ignore!" << std::endl;  
	}  
	void do_write()  
	{  
		boost::mutex::scoped_lock locker(mutex_queue_);  
		if (queue_write_.empty()) // no message to write, clear sending flag  
		{  
			flag_sending_.clear();  
			std::cout << "empty write queue, do_write exit!" << std::endl;  
			return;  
		}  
		write_buf_ = queue_write_.front();  
		queue_write_.pop();  
		// Read a message into our buffer  
		std::cout << ">>>  async write start...." << std::endl;  
		ws_.async_write(net::buffer(write_buf_), beast::bind_front_handler(&session::on_write, shared_from_this()));  
	}  
	void on_write(beast::error_code ec, std::size_t bytes_transferred)  
	{  
		std::cout << ">>>  async write complete, write sized:" << bytes_transferred << std::endl;  
		if (ec || !bytes_transferred)  
			return fail(ec, "write");  
		do_write();  
	}  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
	net::io_context& ioc_;  
	ssl::context& ctx_;  
	tcp::acceptor acceptor_;  
  
public:  
	listener(net::io_context& ioc,ssl::context& ctx,tcp::endpoint endpoint) :  
		ioc_(ioc), ctx_(ctx), acceptor_(net::make_strand(ioc))  
	{  
		beast::error_code ec;  
  
		// Open the acceptor  
		acceptor_.open(endpoint.protocol(), ec);  
		if (ec)  
		{  
			fail(ec, "open");  
			return;  
		}  
  
		// Allow address reuse  
		acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
		if (ec)  
		{  
			fail(ec, "set_option");  
			return;  
		}  
  
		// Bind to the server address  
		acceptor_.bind(endpoint, ec);  
		if (ec)  
		{  
			fail(ec, "bind");  
			return;  
		}  
  
		// Start listening for connections  
		acceptor_.listen(net::socket_base::max_listen_connections, ec);  
		if (ec)  
		{  
			fail(ec, "listen");  
			return;  
		}  
	}  
  
	// Start accepting incoming connections  
	void run()  
	{  
		do_accept();  
	}  
  
private:  
	void do_accept()  
	{  
		// The new connection gets its own strand  
		acceptor_.async_accept(ioc_,  
			beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
	}  
  
	void on_accept(beast::error_code ec, tcp::socket socket)  
	{  
		if (ec)  
		{  
			fail(ec, "accept");  
			return;  
		}  
		else  
		{  
			// Create the session and run it  
			std::make_shared<session>(std::move(socket), ctx_)->run();  
		}  
  
		// Accept another connection  
		do_accept();  
	}  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
	// Check command line arguments.  
	if (argc != 4)  
	{  
		std::cerr <<  
			"Usage: websocket-server-async-ssl <address> <port> <threads>\n" <<  
			"Example:\n" <<  
			"    websocket-server-async-ssl 0.0.0.0 8080 1\n";  
		return EXIT_FAILURE;  
	}  
	auto const address = net::ip::make_address(argv[1]);  
	auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
	auto const threads = std::max<int>(1, std::atoi(argv[3]));  
  
	// The io_context is required for all I/O  
	net::io_context ioc{ threads };  
  
	// The SSL context is required, and holds certificates  
	ssl::context ctx{ ssl::context::tlsv12 };  
  
	// This holds the self-signed certificate used by the server  
	load_server_certificate(ctx);  
  
	// Create and launch a listening port  
	std::make_shared<listener>(ioc, ctx, tcp::endpoint{ address, port })->run();  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
			{  
				ioc.run();  
			});  
	ioc.run();  
  
	return EXIT_SUCCESS;  
}  
```  
run with args :  0.0.0.0 7892 4  
  
Send multiple messages continuously using postman, message size is about 7MB，assert failed  
  
![image](https://user-images.githubusercontent.com/1514920/163594477-9d7c9766-5604-41f7-bd29-1ba16ef816da.png)  
  
My question is that there is no simultaneously read/write, why assert failed without strand?  
  
Use websocket without SSL, the same code has no problem without strand.  
  
Thanks

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-04-15 16:46:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1100221397  

Because SSL reads and writes at the same time as part of the protocol.

---

## Comment 5

> Username: raidenluikang  
> Created at: 2023-04-02 17:27:42 UTC  
> Updated at: 2023-04-02 17:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1493397096  

How to use `boost:;asio::strand` for HTTP client socket   `boost::beast::ssl_stream<boost::beast::tcp_stream>` ?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-04-02 19:46:29 UTC  
> Updated at: 2023-04-02 19:46:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2404#issuecomment-1493424524  

The same way you use a strand with anything
