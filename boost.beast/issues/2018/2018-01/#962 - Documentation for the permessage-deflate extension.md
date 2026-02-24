# #962 - Documentation for the permessage-deflate extension [Closed]

> Username: liuaifu  
> Created at: 2018-01-02 03:29:30 UTC  
> Updated at: 2024-02-14 07:39:42 UTC  
> Closed at: 2024-02-14 07:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/962  

I use boost1.66.0 release develop a websocket server program, the data between client and server is json string, i want to compress it and not find example, is beast support this requirement?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-02 03:45:05 UTC  
> Updated at: 2018-01-02 03:45:51 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-354699423  

Do you mean you want to enable the permessage-deflate extension? Use `stream::set_option` for that:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option.html  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__permessage_deflate.html  
  
By default, streams operating in the server mode have the extension disabled. You can enable it thusly:  
```  
permessage_deflate opt;  
opt.client_enable = true; // for clients  
opt.server_enable = true; // for servers  
ws.set_option(opt);  
```  
  
The documentation could do a better job explaining this.

---

## Comment 2

> Username: liuaifu  
> Created at: 2018-01-02 05:59:41 UTC  
> Updated at: 2018-01-02 06:03:40 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-354707093  

Yes, i want to use permessage-deflate extension. But i run wireshark to check, it seems no effects.  
  
	void Client::run()  
	{  
		// Accept the websocket handshake  
		ws_.async_accept(  
			boost::asio::bind_executor(  
				strand_,  
				std::bind(  
					&Client::on_accept,  
					shared_from_this(),  
					std::placeholders::_1)));  
	}  
	  
	void Client::on_accept(boost::system::error_code ec)  
	{  
		if (ec) {  
			printf("%s: %s\n", __FUNCTION__, ec.message().c_str());  
			if (ws_.next_layer().is_open())  
				ws_.next_layer().cancel();  
			return;  
		}  
	  
		boost::beast::websocket::permessage_deflate opt;  
		opt.client_enable = true; // for clients  
		opt.server_enable = true; // for servers  
		ws_.set_option(opt);  
		  
		//...  
	}

---

## Comment 3

> Username: liuaifu  
> Created at: 2018-01-02 07:19:58 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-354713101  

I move it to the front of ws_.async_accept, now work correctly.  
  
	boost::beast::websocket::permessage_deflate opt;  
	opt.client_enable = true; // for clients  
	opt.server_enable = true; // for servers  
	ws_.set_option(opt);  
	ws_.async_accept(  
		boost::asio::bind_executor(  
			strand_,  
			std::bind(  
				&Client::on_accept,  
				shared_from_this(),  
				std::placeholders::_1)));  
	//...  
  
@vinniefalco Thank you.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-02 14:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-354781484  

Sorry...yes, you have to set the option before performing the handshake. I'm going to go ahead and keep this issue open as a reminder that I need to update the documentation to make this more clear - thanks!

---

## Comment 5

> Username: lls2wow  
> Created at: 2018-03-16 01:08:01 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-373571252  

So the document of permessage-deflate is closed now?   
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option.html  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__permessage_deflate.html

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-03-16 02:02:29 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-373580201  

I don't understand the question?

---

## Comment 7

> Username: hoditohod  
> Created at: 2018-12-04 12:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-444091189  

Hi Vinnie,  
Once the handshake is complete (and compression is enabled), is it possible to selectively enable compression on each individual message?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-12-04 13:36:44 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-444101753  

Compression cannot be selectively enabled and disabled on individual messages yet.

---

## Comment 9

> Username: hoditohod  
> Created at: 2018-12-04 13:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-444104513  

Thanks!

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:36:40 UTC  
> Url: https://github.com/boostorg/beast/issues/962#issuecomment-1256880618  

is this resolved?
