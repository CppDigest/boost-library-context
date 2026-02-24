# #2824 - Memory Leak and Random Closed SSL Websocket [Closed]

> Username: MasterBe  
> Created at: 2024-02-17 17:14:09 UTC  
> Updated at: 2024-02-18 16:18:42 UTC  
> Closed at: 2024-02-17 20:21:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2824  

Hello,   
  
I am getting complaining about memory leak and closed stream which I did not expect. Please if you could any make changes to make this perfect it would I would be very grateful, anything you see not right.  
  
  
```  
#include <chrono>  
#include <iostream>  
#include <thread>  
#include <boost/beast.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/certify/https_verification.hpp>  
  
  
int main(int argc, char *argv[])  
{  
	struct details  
	{  
		std::string host;  
		std::string port;  
		std::string target;  
		std::string body;  
	};  
  
	class socket : public std::enable_shared_from_this<socket>  
	{  
		using ws_stream = boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>;  
	public:  
		inline socket(details dtls) : m_dtls(std::move(dtls)), m_ssl(boost::asio::ssl::context::sslv23_client), m_resolver{ m_ctx }  
		{  
			m_ssl.set_verify_mode(boost::asio::ssl::verify_peer);  
			m_ssl.set_default_verify_paths();  
			boost::certify::enable_native_https_server_verification(m_ssl);  
		}  
  
		void run()  
		{  
			open();  
			m_ctx.run();  
		}  
  
		void open()  
		{  
			m_stream = std::make_unique<ws_stream>(m_ctx, m_ssl);  
			m_resolver.async_resolve(  
				m_dtls.host,  
				m_dtls.port,  
				boost::beast::bind_front_handler(  
					&socket::on_resolve,  
					shared_from_this()));  
		}  
  
		void on_resolve(boost::system::error_code ec, boost::asio::ip::tcp::resolver::results_type results)  
		{  
			if (ec) throw(ec.message());  
			SSL_set_tlsext_host_name(m_stream->next_layer().native_handle(), m_dtls.host.c_str());  
			boost::asio::async_connect(  
				boost::beast::get_lowest_layer(*m_stream),  
				results,  
				boost::beast::bind_front_handler(  
					&socket::on_connect,  
					shared_from_this()));  
		};  
  
		void on_connect(boost::beast::error_code ec, boost::asio::ip::tcp::resolver::results_type::endpoint_type)  
		{  
			if (ec) throw(ec.message());  
			m_stream->control_callback(  
				[socket = shared_from_this()](boost::beast::websocket::frame_type kind, boost::beast::string_view payload) mutable  
			{  
				(void)kind; (void)payload;  
				socket->m_stream->async_pong(  
					boost::beast::websocket::ping_data{},  
					[](boost::beast::error_code ec) { (void)ec; });  
			});  
  
			m_stream->next_layer().async_handshake(boost::asio::ssl::stream_base::client,  
				boost::beast::bind_front_handler(  
					&socket::on_ssl_handshake,  
					shared_from_this()));  
		}  
  
		void on_ssl_handshake(boost::system::error_code ec)  
		{  
			if (ec) throw(ec.message());  
			m_stream->async_handshake(  
				m_dtls.host,  
				m_dtls.target,  
				boost::beast::bind_front_handler(  
					&socket::on_ws_handshake,  
					shared_from_this()));  
		}  
  
		void on_ws_handshake(boost::system::error_code ec)  
		{  
			if (ec) throw(ec.message());  
			m_stream->async_write(  
				boost::asio::buffer(m_dtls.body),  
				boost::beast::bind_front_handler(  
					&socket::on_write,  
					shared_from_this()));  
		};  
  
		void on_write(boost::beast::error_code ec, size_t bytes_transferred)  
		{  
			boost::ignore_unused(bytes_transferred);  
			if (ec) throw(ec.message());  
			async_read();  
		}  
  
		void async_read()  
		{  
			m_stream->async_read(  
				m_buffer,  
				boost::beast::bind_front_handler(  
					&socket::on_read,  
					shared_from_this()));  
		}  
  
		void on_read(boost::beast::error_code ec, size_t bytes_transferred)  
		{  
			boost::ignore_unused(bytes_transferred);  
			if (ec) throw(ec.message());  
			else  
			{  
				std::string message = boost::beast::buffers_to_string(m_buffer.data());  
				std::cout << message << std::endl;  
				m_buffer.consume(m_buffer.size());  
			};  
			async_read();  
		}  
  
	private:  
		boost::asio::io_context	m_ctx;  
		boost::asio::ssl::context m_ssl;  
		boost::asio::ip::tcp::resolver m_resolver;  
		std::unique_ptr<ws_stream> m_stream;  
		boost::beast::flat_buffer m_buffer;  
		details m_dtls;  
	};  
}  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-17 17:51:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2824#issuecomment-1950268419  

Is error message in error.txt generated from this sample code? I didn't find anything obviously wrong with this code.

---

## Comment 2

> Username: MasterBe  
> Created at: 2024-02-17 17:56:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2824#issuecomment-1950269522  

Yes, it's from the exact code! I think the code is runnable to check

---

## Comment 3

> Username: ashtum  
> Created at: 2024-02-17 19:59:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2824#issuecomment-1950295617  

The error related to memory leak might be due to libcrypto and completely unrelated to why the stream closes.  
If you want to throw an error code, you can use the following method instead:  
```cpp  
if (ec)  
    throw boost::system::system_error{ec};  
```  
  
Catch it in the main to see what the error is:  
```cpp  
try  
{  
    ws->run();  
}  
catch (const std::exception& e)  
{  
    std::cout << e.what() << std::endl;  
}  
```

---

## Comment 4

> Username: ashtum  
> Created at: 2024-02-17 20:17:05 UTC  
> Updated at: 2024-02-17 20:18:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2824#issuecomment-1950298909  

It seems the target expects a proper message from you. Sending a message like the following works fine:  
  
```C++  
dtls.body = R"({  
	"method": "SUBSCRIBE",  
	"params": [  
		"btcusdt@aggTrade",  
		"btcusdt@depth"  
	],  
	"id": 1  
	})";  
```  
Alternatively, you can skip sending anything and initiate a read (by calling `async_read()` inside `on_ws_handshake`).

---

## Comment 5

> Username: MasterBe  
> Created at: 2024-02-17 20:21:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2824#issuecomment-1950299659  

problem solved. Thanks so much!
