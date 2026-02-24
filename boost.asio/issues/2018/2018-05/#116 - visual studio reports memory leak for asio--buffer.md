# #116 - visual studio reports memory leak for asio::buffer [Closed]

> Username: snahmad  
> Created at: 2018-05-22 21:05:07 UTC  
> Updated at: 2020-12-30 00:51:37 UTC  
> Closed at: 2020-12-30 00:51:36 UTC  
> Url: https://github.com/boostorg/asio/issues/116  

{214747} normal block at 0x03B52E90, 8 bytes long.  
Data: < N > 88 D3 4E 00 00 00 00 00  
{214746} normal block at 0x03B53210, 8 bytes long.  
Data: 6C D3 4E 00 00 00 00 00  
{214745} normal block at 0x03B53130, 8 bytes long.  
Data:  
  
50 D3 4E 00 00 00 00 00  
{214744} normal block at 0x03B53168, 8 bytes long.  
Data: <4 N > 34 D3 4E 00 00 00 00 00  
{214743} normal block at 0x03B52950, 8 bytes long.  
Data: < N > EC D2 4E 00 00 00 00 00  
{214742} normal block at 0x03B535C8, 8 bytes long.  
Data: < N > D0 D2 4E 00 00 00 00 00  
{214741} normal block at 0x03B52FA8, 8 bytes long.  
Data: < N > B4 D2 4E 00 00 00 00 00  
{214740} normal block at 0x03B529F8, 8 bytes long.  
Data: < N > 98 D2 4E 00 00 00 00 00  
{214739} normal block at 0x004ED298, 288 bytes long.  
Data: < ) promux-651 > F8 29 B5 03 70 72 6F 6D 75 78 2D 36 35 31 00 CD  
  
My code is here. Visual studio 2015.  
buffer_ contents shows as memory leak.  
  
```  
class MulticastSocket   
{  
public:  
	MulticastSocket();  
	virtual ~MulticastSocket();  
	virtual void open(int port_num, const char* network_type="ipv4", const char* ip_address="", uint32_t listen_interface=0);  
	virtual void send(u8* message, u32 size);  
	virtual void close();  
	virtual bool isOpen();  
	//virtual void onReceived(u8*, u32);  
  
private:  
	void write();  
	void strand_push(std::string str);  
  
	void startReceive();  
	void handleReceive(const boost::system::error_code&, std::size_t);  
	void handleSend(const boost::system::error_code& error, std::size_t bytes_transferred);  
  
private:  
	boost::asio::io_service send_ios_;  
	boost::asio::io_service::strand strand_;  
	std::unique_ptr<boost::asio::io_service::work> send_worker_;  
	  
	boost::asio::io_service receive_ios_;  
	std::unique_ptr<boost::asio::io_service::work> receive_worker_;  
  
	boost::thread send_thread_;  
	boost::thread receive_thread_;  
	boost::array<char, 1024> buffer_;  
	boost::asio::ip::udp::endpoint mcast_endpoint_;  
	boost::asio::ip::udp::endpoint sender_endpoint_;  
	boost::asio::ip::udp::endpoint listen_endpoint_;  
  
	std::unique_ptr<boost::asio::ip::udp::socket> send_udp_socket_;  
	std::unique_ptr<boost::asio::ip::udp::socket> receive_udp_socket_;  
	std::deque<std::string>  write_queue_;  
};  
  
  
MulticastSocket::MulticastSocket()  
	: send_ios_()  
	, strand_(send_ios_)  
{  
}  
MulticastSocket::~MulticastSocket()  
{  
}  
  
/***************************************************************************//**  
\brief			  
\details		  
\note			  
\param[in]		  
*******************************************************************************/  
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
		exp;  
		return;  
	}  
}  
  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
ARLErrorCode_e MulticastSocket::close(void)  
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
		e;  
		return;  
	}  
	  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
bool MulticastSocket::isOpen()  
{  
	bool sender = send_udp_socket_ && send_udp_socket_->is_open();  
	bool receive = receive_udp_socket_ && receive_udp_socket_->is_open();  
	return sender && receive;  
}  
  
/***************************************************************************//**  
\brief		Send a message.		  
\details	The message is sent asynchronously.	  
\param		message  
\param		message size  
*******************************************************************************/  
void MulticastSocket::send(u8* message, u32 size)  
{  
	if (!isOpen()) {  
		return;  
	}  
  
	strand_.post(boost::bind(&MulticastSocket::strand_push, this, std::string((char*)message, size)));  
	  
	return;  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
void MulticastSocket::strand_push(std::string str)  
{  
	write_queue_.push_front(str);  
	write();  
}  
/***************************************************************************//**  
\brief			  
\details		  
*******************************************************************************/  
void MulticastSocket::write()  
{  
	std::string buffer = write_queue_.front();  
	write_queue_.pop_front();  
  
	std::string send_to_address = mcast_endpoint_.address().to_string();  
  
	if (send_udp_socket_) {  
		send_udp_socket_->async_send_to(  
			boost::asio::buffer(buffer.c_str(), buffer.size()),  
			mcast_endpoint_,  
			bind(  
				&MulticastSocket::handleSend,  
				this,  
				asio::placeholders::error,  
				asio::placeholders::bytes_transferred));  
	}  
}  
  
void MulticastSocket::handleSend(const system::error_code& error, size_t)  
{  
	if (error)   
	{  
	}  
}  
/***************************************************************************//**  
\brief	Start an asynchronous receiver.			  
*******************************************************************************/  
void NetLib::MulticastSocket::startReceive()  
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
  
void NetLib::MulticastSocket::handleReceive(const system::error_code& error, size_t size)  
{  
	if (!error || error == error::message_size)  
	{  
		startReceive();  
	}  
	else 		  
	{  
	}  
}  
```

---

## Comment 1

> Username: snahmad  
> Created at: 2018-05-23 13:17:47 UTC  
> Url: https://github.com/boostorg/asio/issues/116#issuecomment-391343045  

Have someone experience memory leak dump in visual studio using boost asio buffer?

---

## Comment 2

> Username: snahmad  
> Created at: 2018-05-29 08:45:00 UTC  
> Url: https://github.com/boostorg/asio/issues/116#issuecomment-392700256  

Not body reply me?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-11 15:13:16 UTC  
> Url: https://github.com/boostorg/asio/issues/116#issuecomment-428993852  

I use Visual Studio all the time. The leak is in your code, not Boost.Asio.

---

## Comment 4

> Username: snahmad  
> Created at: 2018-10-11 19:37:31 UTC  
> Url: https://github.com/boostorg/asio/issues/116#issuecomment-429090737  

ok thanks.

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:51:35 UTC  
> Url: https://github.com/boostorg/asio/issues/116#issuecomment-752289569  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#620](https://github.com/chriskohlhoff/asio/issues/620).
