# #1003 - Multiple websocket instances can not exist in a process [Closed]

> Username: lls2wow  
> Created at: 2018-02-01 09:50:21 UTC  
> Updated at: 2018-02-16 19:14:33 UTC  
> Closed at: 2018-02-16 19:14:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1003  

Hi , @vinniefalco   
      I found that if multiple websocket client instances exist in one process. All instance can write message to server , but only one instance can receive message from server. Is there something I did not notice?   
      Note： Every instance owned a io_service. And I Start ios in a single thread like this ：  
      'thr_ = boost::make_shared<boost::thread>(boost::bind(&boost::asio::io_service::run, &ios_));'  
  
      Could you give me some advices thank you?  
  
Br linsong

---

## Comment 1

> Username: lls2wow  
> Created at: 2018-02-01 10:26:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1003#issuecomment-362223504  

I handled read one msg from server and arrange next read task immediately.  
  
```  
void wssession::handle_read(const boost::system::error_code& error, size_t bytes_transferred)  
{  
	//std::cout << "handle_read" << std::endl;  
	if (!error)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		sig_packet( getStrFromMultiBuffer(), bytes_transferred);  
  
		buffer_.consume(buffer_.size());  
		socket_.async_read(buffer_,  
			boost::bind(&wssession::handle_read, shared_from_this(),  
				boost::asio::placeholders::error,  
				boost::asio::placeholders::bytes_transferred));  
	}  
	else  
	{  
		do_close();  
	}  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-01 13:45:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1003#issuecomment-362270066  

You can have as many websocket streams in the same process as you like, and they will all be able to send and receive messages. You do not need one `io_service` per stream, you can use a single `io_service` for your process. They will all receive messages, so I'm not sure what problem you're having.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-15 20:05:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1003#issuecomment-366045684  

Has this issue been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-16 19:14:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1003#issuecomment-366331572  

It looks like there is nothing actionable here so I'll go ahead and close this issue. If you have another problem or question feel free to open a new issue - thanks!
