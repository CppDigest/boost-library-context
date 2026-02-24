# #2510 - Websocket sends tcp rst [Closed]

> Username: XxprogrammatorxX  
> Created at: 2022-08-26 10:15:14 UTC  
> Updated at: 2023-12-31 10:13:51 UTC  
> Closed at: 2023-12-31 10:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2510  

Hello!  
My program consist of 2 simple websocket clients with ssl, that read some data from remote server. Server send ping every 5 seconds and my program must response with pong in next 5 seconds. I tried both sync and async implementation and have the same result.  
  
So, how its look like. Clients starting read data, ping-pong is working(server drops connection in 15s, if not), but after 5-10 minutes one of the clients receive ping and trying to response with pong, but have `boost::system_error "Connection reset by peer"` or `"Broken pipe"` on write.   
  
>  {"ping":1661506057018}  
> boost::syserr : dest : {"pong":1661506057018}  
> Connection reset by peer  
> wss1  
  
  
  
Wireshark said that clients send `TCP RST` but I cant figure out the reason.  
![Screenshot from 2022-08-24 20-16-20](https://user-images.githubusercontent.com/61906529/186877270-663c2476-43ae-4682-b421-b37b423d00bc.png)  
  
  
I'm using gcc, c++17 and Boost 1.75.0 on Ubuntu-20.04LTS. Tested also on other Linux computers from other location and internet providers.  
  
Here's sync websocket implementation:  
```  
class WebSocket : public std::enable_shared_from_this<WebSocket>  
{  
protected:  
	tcp::resolver resolver_;  
	websocket::stream<beast::ssl_stream<beast::tcp_stream>> ws_;  
	websocket::response_type res;  
	beast::flat_buffer buffer_;  
          
        std::string host_;  
	std::string port_;  
	std::string realtime_dir_;  
	std::vector<std::string> subs_;  
  
	websocket::permessage_deflate opt;  
  
public:  
	explicit WebSocket(net::io_context& ioc, ssl::context& ctx)   
	:  
		resolver_(ioc),  
		ws_(net::make_strand(ioc), ctx)  
        {}  
  
	void run(const std::string& host, const std::string& realtime_dir, const std::string& port, const std::vector<std::string>& subs)  
	{  
		// Save these for later  
		opt.client_enable = true;  
  
		host_ = host;  
		port_ = port;  
		realtime_dir_ = realtime_dir;  
		subs_ = subs;  
  
		if (!subs.size())  
			throw std::runtime_error{"no subscribe topics found"};  
  
		// Look up the domain name  
		auto results = resolver_.resolve(host_, port_);  
  
		// Make the connection on the IP address we get from a lookup  
		beast::get_lowest_layer(ws_).connect(results);  
  
		// Set SNI Hostname (many hosts need this to handshake successfully)  
		if (!SSL_set_tlsext_host_name(  
			ws_.next_layer().native_handle(),  
			host_.c_str()))  
		{  
			auto ec = beast::error_code(static_cast<int>(::ERR_get_error()),  
				net::error::get_ssl_category());  
			return fail(ec, "connect");  
		}  
  
		// Perform the SSL handshake  
		ws_.next_layer().handshake(ssl::stream_base::client);  
  
		// Set suggested timeout settings for the Basewebsocket  
		ws_.set_option(websocket::stream_base::timeout::suggested(beast::role_type::client)); // (?)  
		ws_.set_option(opt);  
  
		// Perform the Basewebsocket handshake  
		ws_.handshake(res, host_, realtime_dir_);  
  
		if (res.result_int() == 429 || res.result_int() == 503)  
			return std::this_thread::sleep_for(std::chrono::seconds (30));  
  
		// Send the message  
		std::cout << res << std::flush;  
  
		ws_.write(net::buffer(subs_[0]));  
  
		// Read a message into our buffer  
		ws_.read(buffer_);  
  
		for (size_t i = 1; i < subs_.size(); i++)  
			write(subs_[i]);  
	}  
  
	int read(int (*(parser)) (const std::string& raw))  
	{  
		std::string dest (4096, 0);  
		buffer_.clear();  
		  
		try  
		{  
  
			size_t bytes_readed = ws_.read(buffer_);  
  
			auto out = gzDecompress (boost::asio::buffer_cast<const char*>(buffer_.data()), bytes_readed, dest.c_str(), 4096);  
			  
			dest.resize(out);  
  
			std::cout << '\n' << dest << std::endl;  
			if (is_ping(dest))  
			{  
				dest = generate_pong(dest);  
				ws_.write(net::buffer(dest));  
				std::cout << '\n' << dest << std::endl;  
				dest.resize(4096, 0);  
				return 1;  
			}  
			return (*parser) (dest);  
		}  
		catch (const boost::system::system_error& e)  
		{  
			std::cerr << "boost::syserr : dest : " << dest << "\n" << e.what() << std::endl;  
			return -1;  
		}  
		catch (const std::system_error& e)  
		{  
			std::cerr << "syserr : dest : " << dest << "\n" << e.what() << std::endl;  
			return -1;  
		}  
		catch (const boost::property_tree::ptree_error& e)  
		{  
			std::cerr << "ptree_err : dest : " << dest << "\n" << e.what() << std::endl;  
			return 1;  
		}  
		catch (...)  
		{  
			std::cerr << dest << std::endl;  
			return -1;  
		}  
	}  
}  
```  
  
And usage:  
```  
net::io_context ioc;  
ssl::context ctx{ ssl::context::tlsv13_client };  
  
load_root_certificates(ctx);  
  
  
wss1 = std::make_shared<WebSocket>(ioc, ctx);  
wss1->run("host", "/dir", "443", std::vector<std::string>{sub});  
  
wss2 = std::make_shared<WebSocket>(ioc, ctx);  
wss2->run("host", "/dir", "443", std::vector<std::string>{sub});  
  
while(true)  
{  
	int err = wss1->read(&parser1);  
	if (err < 0)  
	{  
		std::cout << "wss1" << std::endl;  
		return;  
	}  
  
	err = wss2->read(&parser2);  
	if (err < 0)  
	{  
		std::cout << "wss2" << std::endl;  
		return;  
	}  
}  
  
```  
  
Thanks for any help!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-26 14:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1228568705  

is this a Websocket ping and pong or is it a custom message?

---

## Comment 2

> Username: XxprogrammatorxX  
> Created at: 2022-08-26 15:12:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1228618199  

Its custom message in json, that designed by server side

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-26 22:42:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1229019339  

if you are communicating with a "real" websocket server, then sync might not work correctly for you. At a minimum you should be using the async APIs, and always have a call to `websocket::stream::async_read` or  `websocket::stream::async_read_some` active.

---

## Comment 4

> Username: XxprogrammatorxX  
> Created at: 2022-08-26 22:50:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1229023529  

Can you please explain whats the difference between sync and async read in this case?

---

## Comment 5

> Username: XxprogrammatorxX  
> Created at: 2022-08-29 20:06:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1230804425  

So, I've tested it in async read mode and again I have the same issue. Only on wss1, may problem be on server side?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-08-29 23:03:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1230956375  

async read will interleave with async write and correctly send websocket ping and pong messages, while the sync versions cannot do that.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:32:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1231414761  

Can you post your async code?

---

## Comment 8

> Username: XxprogrammatorxX  
> Created at: 2022-08-30 16:51:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1231920036  

Async read:  
  
  
```  
void WebSocket::read()  
{  
	buffer_.clear();  
	  
	ws_.async_read(buffer_, beast::bind_front_handler(&WebSocket::on_message_read, shared_from_this()));  
}  
  
void WebSocket::on_message_read(beast::error_code ec, std::size_t bytes_transferred) {  
	if (ec) return fail(ec, "read");  
	  
	std::string dest (4096, 0);  
	try  
	{  
		auto out = gzDecompress (boost::asio::buffer_cast<const char*>(buffer_.data()), bytes_transferred, dest.c_str(), 4096);  
		  
		dest.resize(out);  
		std::cout << '\n' << dest << std::endl;  
  
		if (is_ping(dest))  
		{  
			dest = generate_pong(dest);  
			std::cout << '\n' << dest << std::endl;  
			ws_.async_write(net::buffer(dest), beast::bind_front_handler(&WebSocket::on_message_write, shared_from_this()));  
			dest.resize(4096, 0);  
		}  
	}  
	catch (const boost::system::system_error& e)  
	{  
		std::cerr << "boost::syserr : dest : " << dest << "\n" << e.what() << std::endl;  
	}  
	catch (const std::system_error& e)  
	{  
		std::cerr << "syserr : dest : " << dest << "\n" << e.what() << std::endl;  
	}  
	catch (const boost::property_tree::ptree_error& e)  
	{  
		std::cerr << "ptree_err : dest : " << dest << "\n" << e.what() << std::endl;  
	}  
	catch (...)  
	{  
		std::cerr << dest << std::endl;  
	}  
}  
  
  
void WebSocket::on_message_write(beast::error_code ec, std::size_t bytes_transferred) {  
	if (ec) return fail(ec, "write");  
}  
```  
  
Usage:  
  
```  
while (true)  
{  
	ioc.reset();  
	wss1->read();  
	wss2->read();  
	ioc.run();  
}  
```  
  
Also I want to mention again, that this ping and pong messages are not websocket's ping and pong. They are required by server and their payload is json with ping or pong values.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2510#issuecomment-1256852151  

I would need a minimal runnable example.
