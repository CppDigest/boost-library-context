# #2691 - Linux connection problems (raspberry pi) [Closed]

> Username: Moerten  
> Created at: 2023-05-22 08:05:15 UTC  
> Updated at: 2023-12-31 08:45:05 UTC  
> Closed at: 2023-12-31 08:45:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2691  

Hi, I am using my socket classes based on the beast (boost 1.81) one's for linux (raspberry pi) now. Having no problems under windows the same classes are most of the time not working under linux. Then only in rare cases the sockets will connect at all.  
  
One problem seems to be that after a reconnect/restart of the server socket/application the socket thinks it is still in use:  
```  
acceptor.open(endpoint.protocol());  
	acceptor.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
	acceptor.set_option(boost::asio::ip::v6_only(false));  
	acceptor.bind(endpoint, errorCode);  
	if (errorCode)  
	{  
		if (errorCode.value() == boost::asio::error::address_in_use)  
		{  
                    ...  
```  
Why is this the case?  
In case the connection does not work I never see the client/server callbacks "on_resolve" and "on_accept" getting called below:  
```  
// Client...  
resolver.async_resolve(  
		{ host.c_str(), port.c_str() },  
		std::bind(  
			&on_resolve,  
			this,  
			std::placeholders::_1,  
			std::placeholders::_2));  
```  
```  
// Server...  
acceptor.async_accept(  
		socket,  
		std::bind(  
			&on_accept,  
			this,  
			std::placeholders::_1));  
```  
Any ideas what could cause these connection issues? Is there something different for windows/linux?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-05-22 09:27:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1556872485  

How do you restart the server? Are you sure you kill the previous process properly

---

## Comment 2

> Username: Moerten  
> Created at: 2023-05-22 09:36:47 UTC  
> Updated at: 2023-05-22 09:39:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1556887128  

The app gets ended normally. That means for the server to execute the acceptor's "close" function and calling "shutdown" for the sessions. I checked with htop the process is not running anymore. But still the socket claims to be in use.

---

## Comment 3

> Username: omartijn  
> Created at: 2023-05-26 08:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1564004959  

Have you tried `SO_REUSEADDR`?

---

## Comment 4

> Username: Moerten  
> Created at: 2023-05-26 11:12:29 UTC  
> Updated at: 2023-05-26 11:14:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1564231965  

Thats what calling  
```  
acceptor.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
```  
should do if I understand correctly. I think the problem is most probably related to that. It seems linux does not understand that the socket isn't used anymore. Maybe this could be because my shutdown is not working correctly or I read the sockets will stay in TIME_WAIT state for some time after shutdown. Also I am wondering why there is no "SO_REUSEPORT" corresponding "reuse_port" option available.

---

## Comment 5

> Username: ashtum  
> Created at: 2023-08-18 17:35:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1684227842  

@Moerten Were you able to successfully resolve the issue?

---

## Comment 6

> Username: Moerten  
> Created at: 2023-08-21 05:43:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1685686676  

Yes, it was necessary to call "restart" for the IO service after each run call and not only before each connect. Under windows this wasn't the case. Also I do not know why you do not call yourself restart in the run function if it is required to do so?

---

## Comment 7

> Username: ashtum  
> Created at: 2023-10-28 16:52:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1783868817  

@Moerten, Having to call restart on io_context suggests that you might have an open acceptor somewhere in the code.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2023-11-24 12:06:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2691#issuecomment-1825581843  

Calling `io_context::restart` is not something to be done casually. Almost no one needs to call this function, and if you do need to call it usually you will have a deep understanding of why.
