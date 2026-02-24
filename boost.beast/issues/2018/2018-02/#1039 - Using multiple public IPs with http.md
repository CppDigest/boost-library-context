# #1039 - Using multiple public IPs with http [Closed]

> Username: Bobface  
> Created at: 2018-02-24 12:27:54 UTC  
> Updated at: 2018-02-26 18:17:01 UTC  
> Closed at: 2018-02-26 18:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1039  

I have a machine which supports multiple public ip-addresses. I am trying to send http requests from that machine using different ip-addresses. To do that, I create the socket like this:  
  
```  
socket = new tcp::socket(io_ctx);  
socket->open(boost::asio::ip::tcp::v4());  
socket->bind(boost::asio::ip::tcp::endpoint(boost::asio::ip::address::from_string("1.2.3." + ip_ending), 0));  
  
// Resolving has been done before and the results are reused for multiple connections  
boost::asio::async_connect(*socket, resolv_results.begin(), resolv_results.end(), std::bind(on_connect, std::placeholders::_1));  
```  
  
As you can see, I am binding to different ip-addresses. `ip_ending` is passed as parameter to that function. When the connection is established, I send the http-request like this:  
  
`http::async_write(*socket, request, std::bind(on_write, std::placeholders::_1, std::placeholders::_2));`  
  
It works and I receive a response. However, looking in the logs of the apache server, I always see the request coming from the same ip-address, although I am using multiple requests with different ip-addresses via `bind`.   
  
I have also created a small client-server program using boost::asio and raw tcp connections. There `bind()` works and the server sees different ips when using `socket.remote_endpoint().address().to_string()`.  
  
So it seems to me that the library is somehow affecting which ip-address is being used for the request. Is that true? And if so, can I change it?   
  
**Edit:**  
  
I have done some more investigation and it seems that the local endpoint is indeed changed. Calling `socket->local_endpoint().address().to_string()` right after `bind()` gives me the correct ip-address.   
  
Making the same call again in the read handler, when the request is finished, gives me a different ip address (the one which shows up in the apache logs).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-24 18:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368248281  

>it seems to me that the library is somehow affecting which ip-address is being used for the request. Is that true?  
  
Impossible. Beast does not use or know about sockets, or even endpoints (IP addresses) for that matter. The exhibited behavior is purely a function of Asio and your operating system.

---

## Comment 2

> Username: Bobface  
> Created at: 2018-02-24 21:59:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368263577  

Thanks for your answer!  
  
I found out that the standalone version (`boost::asio::async_connect`) closes and opens the socket again internally. I had to use the socket's member function (`socket.async_connect`) and now it works.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-24 22:12:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368264346  

That sounds like a bug, maybe open an issue here? https://github.com/boostorg/asio/issues

---

## Comment 4

> Username: Bobface  
> Created at: 2018-02-24 23:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368269854  

I also thought it was a bug first, but after having a look into the [documentation](http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/async_connect/overload3.html) I saw that it is mentioned there:  
  
> Parameters  
>   
> s  
>   
>     The socket to be connected. If the socket is already open, it will be closed.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-02-25 00:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368272524  

There must be a reason, probably because of the overload of `connect` that takes a range of endpoints

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-26 18:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1039#issuecomment-368596625  

It looks like this has been resolved so I'll go ahead and close the issue, feel free to open a new one if needed, thanks!
