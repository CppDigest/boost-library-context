# #951 - Invalid argument [Closed]

> Username: liuaifu  
> Created at: 2017-12-25 01:25:56 UTC  
> Updated at: 2017-12-25 05:34:56 UTC  
> Closed at: 2017-12-25 05:34:56 UTC  
> Url: https://github.com/boostorg/beast/issues/951  

I kill my app and start it soon, the log report 'Invalid argument'. But if i wait seveval seconds and start it, no problem.  
  
### Version of Beast  
boost 1.66.0 release  
  
### Steps necessary to reproduce the problem  
  
```  
listener(  
	boost::asio::io_context& ioc,  
	tcp::endpoint endpoint)  
	: acceptor_(ioc)  
	, socket_(ioc)  
{  
	boost::system::error_code ec;  
  
	// Open the acceptor  
	acceptor_.open(endpoint.protocol(), ec);  
	if (ec)  
	{  
		BOOST_LOG_TRIVIAL(fatal) << "open acceptor fail! " << ec.message();  
		return;  
	}  
	acceptor_.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
	// Bind to the server address  
	acceptor_.bind(endpoint, ec);  
	if (ec)  
	{  
		BOOST_LOG_TRIVIAL(fatal) << "bind fail! " << ec.message();  
		return;  
	}  
  
	// Start listening for connections  
	acceptor_.listen(boost::asio::socket_base::max_listen_connections, ec);  
	if (ec)  
	{  
		BOOST_LOG_TRIVIAL(fatal) << "listen fail! " << ec.message();  
		return;  
	}  
}  
  
//.........  
  
void do_accept()  
{  
	acceptor_.async_accept(  
		socket_,  
		std::bind(  
			&listener::on_accept,  
			shared_from_this(),  
			std::placeholders::_1));  
}  
  
void on_accept(const boost::system::error_code& ec)  
{  
	if (ec)  
	{  
		BOOST_LOG_TRIVIAL(error) << "accep fail! " << ec.message();  
	}  
	else  
	{  
		// Create the session and run it  
		boost::make_shared<Client>(std::move(socket_))->run();  
	}  
  
	// Accept another connection  
	do_accept();  
}  
```  
  
### All relevant compiler information  
gcc 6.3  
  
### Log  
2017-12-21 08:16:31.572823 <0x0000000000a6d8c0> <fatal> bind fail! Address already in use  
2017-12-21 08:16:31.573060 <0x0000000000a6d8c0> <debug> listening on 0.0.0.0:10004  
2017-12-21 08:16:31.573116 <0x0000000000a6d8c0> <error> accep fail! Invalid argument  
2017-12-21 08:16:31.573170 <0x0000000000a6d8c0> <error> accep fail! Invalid argument  
2017-12-21 08:16:31.573217 <0x0000000000a6d8c0> <error> accep fail! Invalid argument

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-25 02:03:25 UTC  
> Url: https://github.com/boostorg/beast/issues/951#issuecomment-353815093  

The answer is in your log:  
```  
2017-12-21 08:16:31.572823 <0x0000000000a6d8c0> bind fail! Address already in use  
```  
  
When you kill the process, the operating system holds on to the port for a little while. Then when you run the program again, the listening socket fails to bind. You can't call `accept` on a listening socket which is unbound.
