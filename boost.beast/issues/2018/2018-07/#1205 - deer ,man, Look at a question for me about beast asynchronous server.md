# #1205 - deer ,man, Look at a question for me about beast asynchronous server [Closed]

> Username: yongs2018  
> Created at: 2018-07-23 06:27:25 UTC  
> Updated at: 2018-07-25 12:48:14 UTC  
> Closed at: 2018-07-25 12:47:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1205  

When I use asynchronous services to implement a subscription, I record session. If I read and write, I find the client peer colse, and I go to delete this session, but the data written by the business thread is still sending, and this time, the ws_.wr_close = = tok is a difference, which is only one of the errors.  
  
thask you . man   
  
Another problem, when i use  
 if (ec)  
	{  
		fail45(ec, "read");  
		del_fd();  
		return ;  
	}  
code ，new data can't read ...  
  
on_read(  
	boost::system::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	// This indicates that the Msession was closed  
	if (ec == websocket::error::closed)  
	{  
		//bsocketProto::instance().remove_websocket(std::atoi(user_id_.c_str()));  
		std::cout<<""<<std::endl;  
		del_fd();  
		return;  
	}  
  
	if (ec)  
	{  
		fail45(ec, "read");  
		del_fd();  
		return ;  
	}  
	proc_request();  
  
I now want to know how to use asynchronous services to subscribe to this function, the key is how to capture the closed connection event, ws_.is_open () seems not enough, my code, here  
  
void Msession::on_read(  
	boost::system::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	// This indicates that the Msession was closed  
	if (ec == websocket::error::closed)  
	{  
		//bsocketProto::instance().remove_websocket(std::atoi(user_id_.c_str()));  
		std::cout<<""<<std::endl;  
		del_fd();  
		return;  
	}  
  
	if (ec)  
	{  
		fail45(ec, "read");  
		del_fd();  
		//return ;  
	}  
        //dispath job thread   
	proc_request();  
	buffer_.consume(buffer_.size());  
	async_read();  
}  
  
//here is job thread call , by std::shared_ptr<Msession>   
bool Msession::push_buffer(const std::string & msg)  
{  
	if( has_fd() == false )  
	{  
		  
		return false;  
	}  
	  
	bool write_pending = false;  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		write_pending = !m_sendQueue.empty();  
		m_sendQueue.emplace_back(msg);  
	}  
  
	if (!write_pending)  
	{  
		async_write();  
	}  
	return true;  
}  
//here is job thread or beast thread call , by std::shared_ptr<Msession>   
void Msession::async_write()  
{  
	try  
	{  
		std::string msg;  
		{  
			std::lock_guard<std::mutex> lock(mtx_);  
			if (m_sendQueue.empty())  
				return;  
			msg = m_sendQueue.front();  
		}  
		auto sp = std::make_shared<std::string>(msg);  
  
		if (ws_.is_open())  
		{  
			ws_.async_write(  
				boost::asio::buffer(*sp), boost::asio::bind_executor(strand_,  
					[sp, self = shared_from_this()](boost::beast::error_code ec, std::size_t bytes_transferred)  
			{  
				self->on_write(ec, bytes_transferred);  
			}));  
		}  
		else  
		{  
			std::cout << "ws.is_open ..." << std::endl;  
		}  
	}  
	catch (boost::exception& es)  
	{  
		std::cout << "boost write exception" << __FILE__ << "||" << __LINE__ << std::endl;  
	}  
	catch (std::exception& es)  
	{  
		std::cout << "std write exception" << __FILE__ << "||" << __LINE__ << std::endl;  
	}  
	catch (...)  
	{  
		std::cout << "... write exception" << __FILE__ << "||" << __LINE__ << std::endl;  
	}  
}  
  
void Msession::on_write(  
	boost::system::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)//error  
	{   
		  
		std::lock_guard<std::mutex> lock(mtx_);  
		m_sendQueue.clear();  
		del_fd();  
		//bsocketProto::instance().remove_websocket(std::atoi(user_id_.c_str()));  
		return fail45(ec, "write");  
	}  
  
	if (!ec)  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		m_sendQueue.pop_front();  
		  
	}  
         
       //beast thread send over call_back  
	async_write();  
	/*  
	std::stringstream ss;  
	ss << boost::beast::buffers(buffer_.data());  
	std::string strData = ss.str();  
  
	std::cout << strData << std::endl;  
	printf("on_write#string:%s,size:%d\n",strData.c_str(), bytes_transferred);  
	*/  
  
}

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-23 16:26:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1205#issuecomment-407118039  

I notice you are trying to do some cleanup type code when the connection is closed. However, the place where you have added these lines:  
  
```  
    bsocketProto::instance().remove_websocket(std::atoi(user_id_.c_str()));  
    ...  
    del_fd();  
```  
  
is not a reliable place. The session can be closed or destroyed without going through those control paths. I suggest instead, that you place your cleanup code in the destructor of the `session` object. This way, you will be guaranteed that the cleanup code will always run exactly once when the session object is destroyed.  
  
As for the other problems you brought up, I do not fully understand the issue. Can you please go into a little more detail, perhaps also with examples?  
  
Thanks

---

## Comment 2

> Username: yongs2018  
> Created at: 2018-07-23 17:52:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1205#issuecomment-407144690  

The same mistake  , my god , can't Solve  
code has modify this ：  
  
bool Msession::send_msg(const std::string & msg)  
{  
	bool write_pending = false;  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		write_pending = !m_sendQueue.empty();  
		m_sendQueue.emplace_back(msg);  
	}  
  
	if (!write_pending)  
	{  
		async_write();  
	}  
	return true;  
}  
  
void Msession::async_write()  
{  
	std::string msg;  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		if (m_sendQueue.empty())  
			return;  
		msg = m_sendQueue.front();  
	}  
	auto sp = std::make_shared<std::string>(msg);  
	//auto b = boost::asio::dynamic_buffer(*sp);  
	  
	ws_.async_write(  
			boost::asio::buffer(*sp), boost::asio::bind_executor(strand_,  
				[sp, self = shared_from_this()](boost::beast::error_code ec, std::size_t bytes_transferred)  
		{  
			self->on_write(ec, bytes_transferred);  
		}));  
}  
  
void  
Msession::on_write(  
	boost::system::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)//error  
	{  
  
		std::lock_guard<std::mutex> lock(mtx_);  
		m_sendQueue.clear();  
		SendBuffer* sq = SendBuffer::getInstance();  
		auto p = shared_from_this();  
		sq->del_fd(p);  
		std::cout << "write close" << std::endl;  
		return;  
	}  
  
	if (!ec)  
	{  
		std::lock_guard<std::mutex> lock(mtx_);  
		std::cout << "m_sendQueue.size()" << m_sendQueue.size() << std::endl;  
		m_sendQueue.pop_front();  
  
	}  
	async_write();  
}  
  
  
Assertion failed: ! base_, file e:\sdkroot\boost_x64\include\boost-1_66\boost\beast\websocket\detail\pausation.hpp, line 213

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-23 18:09:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1205#issuecomment-407150068  

It is hard to read the code because it is not formatted, try using triple backticks, "```", on a line above and on a line below the code:  
```  
    ```  
    (code)  
    ```  
```

---

## Comment 4

> Username: yongs2018  
> Created at: 2018-07-25 12:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1205#issuecomment-407742616  

good
