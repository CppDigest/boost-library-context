# #1246 - About NetWrok Blocking problem by using beast [Closed]

> Username: lls2wow  
> Created at: 2018-09-17 14:29:02 UTC  
> Updated at: 2018-09-18 04:30:28 UTC  
> Closed at: 2018-09-18 04:30:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1246  

Hi , @vinniefalco   
      Trouble you again , Rencently I use beast write many net tools from my work. Like websocket, http , using ssl or not. but every program has a same problem . When I run much instances of my program. Sometimes later , the entire local area network will be blocked.(with very small flow) Either the web browser can`t work. But I closed my program all , network get to work. Reflected to my code , for some reason I always create the websocket or http client frequently.   
  
       Like the http sync ,   
```  
CHttpClient::~CHttpClient()  
{  
	boost::system::error_code ec;  
	sock.shutdown(tcp::socket::shutdown_both, ec);  
}  
```  
  
 Or websocket code ：  
```  
void wssession::on_close(boost::system::error_code ec)  
{  
// 	boost::system::error_code ecc;  
// 	socket_.next_layer().shutdown(boost::asio::socket_base::shutdown_both, ecc);  
// 	socket_.next_layer().close();  
  
	async_teardown(role_type::client, socket_.next_layer(), std::bind(  
		&wssession::on_teardown,  
		shared_from_this(),  
		std::placeholders::_1));  
}  
  
void wssession::on_teardown(boost::system::error_code ec)  
{  
	state_ = state_close;  
	sig_close();  
}  
  
```  
  
I always remember to close the socket resource , But it seems not work good.   
  
when there`s many Network requests, net will be blocked.  
Is there something wrong from my code, or any other resources I did not handle?

---

## Comment 1

> Username: lls2wow  
> Created at: 2018-09-17 14:31:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1246#issuecomment-422040484  

Only shallow, I think I did not describe the problem correctly, could you help me ?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-09-17 18:27:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1246#issuecomment-422121129  

I have no ideas
