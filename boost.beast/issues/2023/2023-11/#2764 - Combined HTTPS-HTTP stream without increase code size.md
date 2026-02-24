# #2764 - Combined HTTPS/HTTP stream without increase code size. [Closed]

> Username: danilopucci  
> Created at: 2023-11-04 14:07:19 UTC  
> Updated at: 2023-11-07 00:24:02 UTC  
> Closed at: 2023-11-04 16:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2764  

Hello, I am building a header-only http-client lib using beast and I need it to be capable of sending both plain and SSL http.   
  
I could do this by using ```boost::beast::ssl_stream<boost::beast::tcp_stream>``` for the SSL and ```boost::beast::tcp_stream``` for the plain. The problem is that the code size grows a lot when I use this approach. I am looking for alternatives and optimizations for this.  
  
I am wondering if there is a way to use only ssl_stream socket and just skip the handshake calls when it is plain HTTP and keep it when it is SSL. I can compile with this approach, the compiled code size is much smaller but it does not works.  
I could experiment multiple variations of implementation, and I could notice that the fact of calling ```boost::beast::http::async_write``` and read with the two streams increase the code size by 60%  
  
  
PS1: I also tried the approach mentioned by @madmongo1 on #1849 of using the ```if (ssl_enabled_)``` and ```stream.next_layer()``` but in the end it is pretty much the same result of using two streams, at least in therms of code size.  
  
PS2: I already cleaned unecessary includes and kept only the necessary. The solution really looks on something towards the direction of simplifying the streams

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-11-04 15:47:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2764#issuecomment-1793480520  

Unfortunately no that is not going to be possible, because of the way that beast HTTP initiating functions are templated on the stream.

---

## Comment 2

> Username: danilopucci  
> Created at: 2023-11-04 16:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2764#issuecomment-1793492577  

Thank you so much!

---

## Comment 3

> Username: danilopucci  
> Created at: 2023-11-07 00:24:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2764#issuecomment-1797075616  

Just a note for whoever be interested in:  
I could achieve my desired result by using ```boost::asio::ip::tcp::socket``` instead of ```boost::beast::tcp_stream``` and ```boost::asio::ssl::stream<boost::asio::ip::tcp::socket&>``` instead of ```boost::beast::ssl_stream<boost::beast::tcp_stream>```.  
  
As my application is a small one, just to fetch and send some data to APIs, I see no downsides using that.   
  
Am I missing something by choosing this approach?
