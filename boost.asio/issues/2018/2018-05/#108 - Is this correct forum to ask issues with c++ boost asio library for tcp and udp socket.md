# #108 - Is this correct forum to ask issues with c++ boost asio library for tcp and udp socket. [Closed]

> Username: snahmad  
> Created at: 2018-05-09 12:18:04 UTC  
> Updated at: 2020-12-30 00:51:04 UTC  
> Closed at: 2020-12-30 00:51:03 UTC  
> Url: https://github.com/boostorg/asio/issues/108  



---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-09 18:49:42 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-387838827  

Just ask your question

---

## Comment 2

> Username: snahmad  
> Created at: 2018-05-10 13:51:22 UTC  
> Updated at: 2018-05-10 14:31:08 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388059320  

Does This TCPClient implementation looks good. I have to use boost::asio::io_service::work to reset/stop to boost::asio::io_service .other it does not work.  
  
This is synchrounous TCP client class. I am looking async TCP Client implmentation as well. also Async TCP Server.   
  
  
  
```  
class TCPClient  
{  
public:  
	TCPClient();  
	virtual ~TCPClient();  
	virtual void open(int port_num, const char* network_type="ipv4", const char* ip_address="");  
	virtual void send(u8* message, u32 size);  
	virtual void close();  
	virtual bool isOpen();  
  
private:  
	void handleSend(const boost::system::error_code& error, std::size_t bytes_transferred);  
  
	void handleRead(const boost::system::error_code&, size_t);  
private:  
	boost::asio::io_service io_service_;  
	std::unique_ptr<boost::asio::io_service::work> worker_;  
	boost::asio::ip::tcp::socket tcp_socket_;  
	std::thread thread_;  
  
	static const unsigned max_length = 1024;  
  
	char data_[max_length];  
};  
/***************************************************************************//**  
\brief			Constructor  
\details		  
			   
*******************************************************************************/  
  
TCPClient::TCPClient ()  
	:	io_service_()  
	,	tcp_socket_(io_service_)  
{  
}  
TCPClient::~TCPClient()  
{  
	worker_.reset();  
	if (thread_.joinable()) {  
		thread_.join();  
	}  
}  
/***************************************************************************//**  
\brief			  
\details		  
\note			  
\param[in]		  
*******************************************************************************/  
void TCPClient::open(int port_num, const char* network_type, const char* ip_address)  
{  
	try  
	{  
		tcp::resolver resolver(io_service_);  
		tcp::resolver::iterator iterator;  
  
		if(strcmp(network_type, "ipv4")==0)  
		{  
			tcp::resolver::query query(tcp::v4(), ip_address, boost::lexical_cast<std::string>(port_num));  
			iterator = resolver.resolve(query);  
		}  
		else if(strcmp(network_type, "ipv6")==0)  
		{  
			tcp::resolver::query query(tcp::v6(), ip_address, boost::lexical_cast<std::string>(port_num));  
			iterator = resolver.resolve(query);  
		}  
		else  
		{	  
			return;  
		}  
		  
		boost::system::error_code ec;  
		boost::asio::connect(tcp_socket_, iterator, ec);  
		if(ec)  
		{  
			std::string msg = ec.message();  
			return;  
		}  
  
		worker_.reset(new boost::asio::io_service::work(io_service_));  
  
		tcp_socket_.async_read_some(boost::asio::buffer(data_, max_length),  
			boost::bind(&TCPClient::handleRead, this,  
				boost::asio::placeholders::error,  
				boost::asio::placeholders::bytes_transferred));  
  
  
		thread_ = std::thread([&]() {  
			io_service_.run();  
		});  
  
		return;  
	}  
	catch (std::exception& e)  
	{  
		std::string error = e.what();  
		return;  
	}  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
void TCPClient::close ()  
{  
	try  
	{  
		if (tcp_socket_.is_open())  
		{  
			//tcp_socket_.cancel();  
			tcp_socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both);  
			tcp_socket_.close();  
		}  
		worker_.reset();  
		//io_service_.stop();  
		if (thread_.joinable()) {  
			thread_.join();  
		}  
	}  
	catch (std::exception& e)  
	{  
		std::string str = e.what();  
		return;  
	}  
	return;  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
bool TCPClient::isOpen()  
{  
	return tcp_socket_.is_open();  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
ARLErrorCode_e TCPClient::send (u8* message, u32 size)  
{  
	try  
	{  
		if(!tcp_socket_.is_open())  
		{  
			return;  
		}  
  
		tcp_socket_.async_write_some(  
			  boost::asio::buffer(message, size),  
			  boost::bind(&TCPClient::handleSend, this,	boost::asio::placeholders::error, boost::asio::placeholders::bytes_transferred));  
	  
  
	  
		return;  
	}  
	catch (std::exception& )  
	{  
		return;  
	}  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
void TCPClient::handleSend(const boost::system::error_code& error,  
      std::size_t bytes_transferred)  
{  
  
}  
  
/***************************************************************************//**  
\brief		Handles a read operation.  
\param		error code  
\param		byte count  
*******************************************************************************/  
void TCPClient::handleRead(const boost::system::error_code& error, size_t bytes_transferred)  
{  
	if (!error)  
	{  
		tcp_socket_.async_read_some(  
			boost::asio::buffer(data_, max_length),  
			boost::bind(&TCPClient::handleRead, this, boost::asio::placeholders::error, boost::asio::placeholders::bytes_transferred));  
	}  
}  
  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-10 14:08:34 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388064392  

1. Please format your code correctly, view GitHub's documentation on Markdown  
  
2. Who manages the lifetime of the buffer passed to `send`?

---

## Comment 4

> Username: snahmad  
> Created at: 2018-05-10 14:37:24 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388073016  

Caller of method send. It gets delete.Is it issue. Does boost::asio::buffer do not make deep copy?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-10 14:56:02 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388078810  

> Does boost::asio::buffer do not make deep copy?  
  
`boost::asio::buffer` does not make a deep copy, that would incur unnecessary memory allocations. It is the caller's responsibilty to manage the lifetime of the buffers:  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/overview/core/buffers.html

---

## Comment 6

> Username: snahmad  
> Created at: 2018-05-10 15:03:02 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388081075  

ok, I delete the buffer after async_write_some call. Is it blocking call? after TCPClient::send  finish then I delete buffer. Is it ok?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-10 15:14:04 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388084412  

`async_write_some` is asynchronous. It is not blocking.

---

## Comment 8

> Username: snahmad  
> Created at: 2018-05-10 16:50:41 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388113585  

oh ok. so I need to keep sender buffer copy in my TCPClient class same as receiver buffer i kept. If multiple calls send after other then async_write_some may not complete. Am I correct. I need mutex as well.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-05-10 16:53:01 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388114253  

No, there are a lot of problems with the code you posted. `async_write_some` is not the correct function, you should be using `async_write`. And you can only have one pending write active at a time.

---

## Comment 10

> Username: snahmad  
> Created at: 2018-05-10 18:55:42 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388150840  

ok. can you point to correct example code please.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-05-10 19:02:56 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388152944  

Lots of examples here:  
https://github.com/boostorg/asio/tree/develop/example  
  
and here:  
https://github.com/boostorg/beast/tree/develop/example

---

## Comment 12

> Username: snahmad  
> Created at: 2018-05-10 19:28:12 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388159530  

ok, sure. will write either synchronous tcp client or full asynchronous tcp client.

---

## Comment 13

> Username: snahmad  
> Created at: 2018-05-10 20:06:31 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388169713  

I need one c++ tcp class which can provide send asynchronous  command and gets response back  within specified timeout similar to HTTP client which post command and wait for response.   
  
I need multicast udp socket class as well for send end asynchronous  command and gets response back  within specified timeout.  
  
I never find any example close this use case. Either class is TCP Server (receive) or TCP client(send).  
  
Does boost beast provides TCP or UDP classes?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-05-10 20:34:27 UTC  
> Updated at: 2018-05-10 20:34:45 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388177109  

What you are asking for is something very specific. You are unlikely to find precisely what you are looking for in an example. I suggest you spend some time understanding Asio a bit better, so that you will have the knowledge to implement exactly what you want. You can look at the Asio and Beast examples for ideas, but ultimately it is your responsibility to figure out how to put those pieces together correctly to address your particular use-case.

---

## Comment 15

> Username: snahmad  
> Created at: 2018-05-10 20:56:01 UTC  
> Updated at: 2018-05-10 21:03:12 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388183128  

sure. I have implement my classes. they are working 99%. occasional. it cause crash during close and reopen. I want some one to review my code. like you did for my TCPClient class. I think for now i can change my async_write to blocking write which will make it robust. I am already receiving reply async fine..   
  
I  already have udp multicast class.  I see only basic examples like udp multicast sender and receiver separate example which are not typically use case in real application.  application normally send command  and receive reply e.g mdns. just my observations.

---

## Comment 16

> Username: snahmad  
> Created at: 2018-05-11 09:07:54 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388307301  

I think I fixed issues.  
Can you code review TCPClient and MulticastSocket class. I still gets memory leaks in visual studio 2015 related usage of boost::asio::buffer.  
I am boost 1.67.  
  
Replace  
```  
tcp_socket_.async_write_some(  
			  boost::asio::buffer(message, size),  
			  boost::bind(&TCPClient::handleSend, this,	boost::asio::placeholders::error, boost::asio::placeholders::bytes_transferred));  
  
```	  
with   
```  
boost::system::error_code error;  
		size_t bytes_written = boost::asio::write(tcp_socket_, boost::asio::buffer(message, size),  
			boost::asio::transfer_all(),  
			error);  
  
```  
I have keep usage of  tcp_socket_.async_read_some   
  
I tried using boost::asio::async_read but I get  error_code in my handleRead.  
  
Same I did in my Multicast UDP.  
  
```  
class MulticastSocket   
{  
public:  
	MulticastSocket();  
	virtual ~MulticastSocket();  
	virtual  open(int port_num, const char* network_type="ipv4", const char* ip_address="", uint32_t listen_interface=0);  
	virtual ARLErrorCode_e send(u8* message, u32 size);  
	virtual ARLErrorCode_e close();  
	virtual bool isOpen();  
	//virtual void onReceived(u8*, u32);  
  
private:  
	void startReceive();  
	void handleReceive(const boost::system::error_code&, std::size_t);  
  
private:  
	boost::asio::io_service send_ios_;  
	std::unique_ptr<boost::asio::io_service::work> send_worker_;  
	  
	boost::asio::io_service receive_ios_;  
	std::unique_ptr<boost::asio::io_service::work> receive_worker_;  
  
	boost::thread send_thread_;  
	boost::thread receive_thread_;  
	boost::array<u8, 1024> buffer_;  
	//enum { max_length = 1024 };  
	//char buffer_[max_length];  
	boost::asio::ip::udp::endpoint mcast_endpoint_;  
	boost::asio::ip::udp::endpoint sender_endpoint_;  
	boost::asio::ip::udp::endpoint listen_endpoint_;  
  
	std::unique_ptr<boost::asio::ip::udp::socket> send_udp_socket_;  
	std::unique_ptr<boost::asio::ip::udp::socket> receive_udp_socket_;  
  
};  
  
```  
  
```  
MulticastSocket::MulticastSocket()  
{  
}  
MulticastSocket::~MulticastSocket()  
{  
}  
void MulticastSocket::open(int port_num, const char* network_type, const char* multicastAddress, uint32_t listen_interface)  
{  
	if (listen_interface == 0) {  
		return;  
	}  
	  
	try  
	{  
		struct in_addr in;  
		in.S_un.S_addr = listen_interface;  
		char* address_listen = inet_ntoa(in);  
		std::string address_mcast = multicastAddress;  
		unsigned short address_port = port_num;  
  
		boost::system::error_code ec;  
  
		boost::asio::ip::address listen_addr = boost::asio::ip::address::from_string(address_listen, ec);  
		boost::asio::ip::address mcast_addr = boost::asio::ip::address::from_string(address_mcast, ec);  
  
		  
		if (strcmp(network_type, "ipv4") == 0)  
		{  
			listen_endpoint_ = udp::endpoint(listen_addr, port_num);  
			mcast_endpoint_ =  udp::endpoint(mcast_addr, port_num);  
		}  
		else if (strcmp(network_type, "ipv6") == 0)  
		{  
			listen_endpoint_ = udp::endpoint(listen_addr, port_num);  
			mcast_endpoint_ = udp::endpoint(mcast_addr, port_num);  
		}  
		else  
			return;  
  
		send_udp_socket_.reset(new boost::asio::ip::udp::socket(send_ios_));  
		receive_udp_socket_.reset(new boost::asio::ip::udp::socket(receive_ios_));  
  
		send_udp_socket_->open(boost::asio::ip::udp::v4());  
		receive_udp_socket_->open(listen_endpoint_.protocol());  
		send_udp_socket_->set_option(boost::asio::ip::udp::socket::reuse_address(true));  
		receive_udp_socket_->set_option(boost::asio::ip::udp::socket::reuse_address(true));  
		  
		boost::asio::ip::address_v4 local_interface =  
			boost::asio::ip::address_v4::from_string(address_listen);  
		boost::asio::ip::multicast::outbound_interface option(local_interface);  
		  
		receive_udp_socket_->bind(listen_endpoint_);  
		  
		// Join the multicast group.  
		receive_udp_socket_->set_option(boost::asio::ip::multicast::join_group(mcast_addr.to_v4(), listen_addr.to_v4()), ec);  
		  
		send_udp_socket_->set_option(option);  
		receive_udp_socket_->set_option(option);  
  
		send_udp_socket_->set_option(asio::ip::multicast::enable_loopback(false));  
  
		//boost::asio::ip::multicast::hops hops_option(3);  
		//send_udp_socket_->set_option(hops_option);  
		//receive_udp_socket_->set_option(hops_option);  
  
		receive_worker_.reset(new boost::asio::io_service::work(receive_ios_));  
		send_worker_.reset(new boost::asio::io_service::work(send_ios_));  
  
		startReceive();  
  
		receive_thread_ = boost::thread(boost::bind(&boost::asio::io_service::run, &receive_ios_));  
		send_thread_ = boost::thread(boost::bind(&boost::asio::io_service::run, &send_ios_));  
  
  
		return;  
	}  
	catch (std::exception& exp)  
	{  
		rtTRACETIMED(" MulticastSocket::open() exception:%s\n", exp.what());  
		std::string str = exp.what();  
		return;  
	}  
}  
  
void MulticastSocket::close(void)  
{  
	try {  
		boost::system::error_code ec;  
		if (send_udp_socket_) {  
			//send_udp_socket_->cancel();  
			send_udp_socket_->shutdown(socket_base::shutdown_both, ec);  
		}  
		if (receive_udp_socket_) {  
			//receive_udp_socket_->cancel();  
			receive_udp_socket_->shutdown(socket_base::shutdown_both, ec);  
		}  
		if (ec)   
		{  
			return;  
		}  
		if (send_udp_socket_ && send_udp_socket_->is_open())  
		{  
			send_udp_socket_->close();  
		}  
		if (receive_udp_socket_ && receive_udp_socket_->is_open())  
		{  
			receive_udp_socket_->close();  
		}  
  
		receive_worker_.reset();  
		send_worker_.reset();  
  
		receive_udp_socket_.reset();  
		send_udp_socket_.reset();  
  
		if (send_thread_.joinable()) {  
			send_thread_.join();  
		}  
		if (receive_thread_.joinable()) {  
			receive_thread_.join();  
		}  
  
	}  
	catch (std::exception& e)  
	{  
		rtTRACETIMED(" MulticastSocket::close() exception:%s\n", e.what());  
		std::string str = e.what();  
		return;  
	}  
	  
	return;  
}  
  
bool MulticastSocket::isOpen()  
{  
	bool sender = send_udp_socket_ && send_udp_socket_->is_open();  
	bool receive = receive_udp_socket_ && receive_udp_socket_->is_open();  
	return sender && receive;  
}  
  
void MulticastSocket::send(u8* message, u32 size)  
{  
	if (!isOpen()) {  
		return;  
	}  
  
	std::string send_to_address = mcast_endpoint_.address().to_string();  
  
	if (send_udp_socket_) {  
		send_udp_socket_->send_to(  
			boost::asio::buffer(message, size),  
			mcast_endpoint_);  
	}  
	return;  
}  
  
void MulticastSocket::startReceive()  
{  
	if (receive_udp_socket_) {  
		receive_udp_socket_->async_receive_from(  
			boost::asio::buffer(buffer_),  
			sender_endpoint_,  
			bind(  
				&MulticastSocket::handleReceive,  
				this,  
				asio::placeholders::error,  
				asio::placeholders::bytes_transferred));  
  
		std::string sender_address = sender_endpoint_.address().to_string();  
	}  
}  
  
void MulticastSocket::handleReceive(const system::error_code& error, size_t size)  
{  
	if (!error || error == error::message_size)  
	{  
		BoostSocketClient::onReceived((u8*)&buffer_[0], size);  
  
		startReceive();  
	}  
	else 		  
	{  
	}  
}  
  
```

---

## Comment 17

> Username: snahmad  
> Created at: 2018-05-11 10:59:44 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388332215  

```  
//send_udp_socket_->cancel();  
//receive_udp_socket_->cancel();  
  
```  
I comment out cancel. Can I use them. It remove memory leaks dump appears in output windows in visual studio 2015. I gets warning as well.  my program supports windows xp. How I can disable warnings.

---

## Comment 18

> Username: snahmad  
> Created at: 2018-05-11 11:22:24 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388336471  

Change from async_write_some to boost::asio::write makes my application command/response slower. some time it timeout. I guess I need to see how I can post using io_service write using buffer/queue

---

## Comment 19

> Username: snahmad  
> Created at: 2018-05-11 14:08:36 UTC  
> Updated at: 2018-05-12 09:13:12 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388374546  

```  
class TCPClient  
{  
public:  
	TCPClient();  
	virtual ~TCPClient();  
	virtual void open(int port_num, const char* network_type="ipv4", const char* ip_address="");  
	virtual void send(u8* message, u32 size);  
	virtual void close();  
	virtual bool isOpen();  
  
private:  
	void handleSend(const boost::system::error_code& error, std::size_t bytes_transferred);  
	void handleRead(const boost::system::error_code&, size_t);  
	void write();  
	void strand_push(std::string str);  
private:  
	boost::asio::io_service io_service_;  
	boost::asio::io_service::strand strand_;  
	std::unique_ptr<boost::asio::io_service::work> worker_;  
	boost::asio::ip::tcp::socket tcp_socket_;  
	std::thread thread_;  
	static const unsigned max_length = 1024;  
	char data_[max_length];  
	std::deque<std::string>  write_queue_;  
};  
  
```  
```  
TCPClient::TCPClient ()  
	:	io_service_()  
	 ,   strand_(io_service_)  
	,	tcp_socket_(io_service_)  
{  
}  
TCPClient::~TCPClient()  
{  
	worker_.reset();  
	if (thread_.joinable()) {  
		thread_.join();  
	}  
}  
void TCPClient::open(int port_num, const char* network_type, const char* ip_address)  
{  
	try  
	{  
		tcp::resolver resolver(io_service_);  
		tcp::resolver::iterator iterator;  
  
		if(strcmp(network_type, "ipv4")==0)  
		{  
			tcp::resolver::query query(tcp::v4(), ip_address, boost::lexical_cast<std::string>(port_num));  
			iterator = resolver.resolve(query);  
		}  
		else if(strcmp(network_type, "ipv6")==0)  
		{  
			tcp::resolver::query query(tcp::v6(), ip_address, boost::lexical_cast<std::string>(port_num));  
			iterator = resolver.resolve(query);  
		}  
		else  
		{	  
			return;  
		}  
		  
		boost::system::error_code ec;  
		boost::asio::connect(tcp_socket_, iterator, ec);  
		if(ec)  
		{  
			std::string msg = ec.message();  
			return;  
		}  
  
		worker_.reset(new boost::asio::io_service::work(io_service_));  
  
		tcp_socket_.async_read_some(boost::asio::buffer(data_, max_length),  
			boost::bind(&TCPClient::handleRead, this,  
				boost::asio::placeholders::error,  
				boost::asio::placeholders::bytes_transferred));  
  
  
		thread_ = std::thread([&]() {  
			io_service_.run();  
		});  
	}  
	catch (std::exception& e)  
	{  
		e;  
	}  
}  
void TCPClient::close ()  
{  
	try  
	{  
		if (tcp_socket_.is_open())  
		{  
			//tcp_socket_.cancel();  
			tcp_socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both);  
			tcp_socket_.close();  
		}  
		worker_.reset();  
		//io_service_.stop();  
		if (thread_.joinable()) {  
			thread_.join();  
		}  
	}  
	catch (std::exception& e)  
	{  
		e;  
	}  
}  
bool TCPClient::isOpen()  
{  
	return tcp_socket_.is_open();  
}  
void TCPClient::send (u8* message, u32 size)  
{  
	try  
	{  
		if(!tcp_socket_.is_open())  
		{  
			return;  
		}  
  
		strand_.post(boost::bind(&TCPClient::strand_push, this, std::string((char*)message, size)));  
	}  
	catch (std::exception& )  
	{  
		return;  
	}  
}  
void TCPClient::strand_push(std::string str)   
{  
	write_queue_.push_front(str);  
	write();  
}  
void TCPClient::write()  
{  
	std::string buffer = write_queue_.front();  
	write_queue_.pop_front();  
boost::asio::async_write(tcp_socket_,  
		boost::asio::buffer(buffer.c_str(), buffer.size()),  
		boost::bind(&TCPClient::handleSend, this, boost::asio::placeholders::error, boost::asio::placeholders::bytes_transferred));  
}  
void TCPClient::handleSend(const boost::system::error_code& error,  
      std::size_t bytes_transferred)  
{  
  
}  
  
/***************************************************************************//**  
\brief		Handles a read operation.  
\param		error code  
\param		byte count  
*******************************************************************************/  
void TCPClient::handleRead(const boost::system::error_code& error, size_t bytes_transferred)  
{  
	if (!error)  
	{  
		tcp_socket_.async_read_some(  
			boost::asio::buffer(data_, max_length),  
			boost::bind(&TCPClient::handleRead, this, boost::asio::placeholders::error, boost::asio::placeholders::bytes_transferred));  
	}  
}  
  
  
```  
  
Can you check my updated TCPClient class. now I using strand and queue of string to write async. Quick code review will be appreciated. It seems working.

---

## Comment 20

> Username: snahmad  
> Created at: 2018-05-11 15:14:07 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388393964  

Remaining issue is connect and disconnect async way.

---

## Comment 21

> Username: snahmad  
> Created at: 2018-05-12 09:13:54 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388541948  

Now I am using   
  
boost::asio::async_write. but for reading async_read_some. Is this correct way?

---

## Comment 22

> Username: snahmad  
> Created at: 2018-05-14 08:33:49 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-388738925  

It is working for me. Any more improvement will be appreciated such as async connect.

---

## Comment 23

> Username: ghost  
> Created at: 2020-12-30 00:51:02 UTC  
> Url: https://github.com/boostorg/asio/issues/108#issuecomment-752289467  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#614](https://github.com/chriskohlhoff/asio/issues/614).
