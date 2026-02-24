# #1221 - sent and received data corrupt in asynchronous method's handler [Closed]

> Username: tensor5375  
> Created at: 2018-08-10 03:56:37 UTC  
> Updated at: 2018-08-11 04:18:15 UTC  
> Closed at: 2018-08-10 11:16:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1221  

I'm writing an asynchronous **WebSocket** client with beast.   
**Correct messages return from server with synchronous method**. But in the asynchronous client's read/write handlers, sent or received data corrupted.  
Why?  
**This does not occur in asynchronous TCP client's socket.**  
  
```  
void Client::send(  
　const string &_mess  
　)  
{  
	char buff[1024] = { 0 };  
	size_t len = _mess.length();  
	memcpy(buff, _mess.data(), _mess.length()); //clean data here  
	web_socket_.async_write(  
　　　　　buffer(buff, _mess.length())  
　　　　　, boost::bind(  
　　　　　　　&Client::on_send  
　　　　　　　, this  
　　　　　　　, asio::placeholders::error  
　　　　　　　, asio::placeholders::bytes_transferred, buff));  
}  
void Client::on_send(  
　const system::error_code& _err  
　, size_t _bytes_transferred  
　, string _data //corrupted here  
　)  
{  
	if (_err)  
	{  
		cout << "send error: " << _err.message() << endl;  
		return;  
	}  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-10 05:28:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1221#issuecomment-411979255  

```  
char buff[1024] = { 0 };  
```  
  
Are you calling `async_write` with a buffer that has automatic storage duration?

---

## Comment 2

> Username: tensor5375  
> Created at: 2018-08-10 07:30:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1221#issuecomment-412000648  

Thanks vinniefalco.  
I'll try with automatic storage.

---

## Comment 3

> Username: tensor5375  
> Created at: 2018-08-10 11:15:59 UTC  
> Updated at: 2018-08-11 04:18:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1221#issuecomment-412053342  

Data corruption resolved by using 'string' for sending and 'streambuf' for receiving.  
Thanks.
