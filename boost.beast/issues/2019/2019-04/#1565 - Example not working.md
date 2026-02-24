# #1565 - Example not working. [Closed]

> Username: csm10495  
> Created at: 2019-04-10 23:02:00 UTC  
> Updated at: 2019-04-10 23:08:37 UTC  
> Closed at: 2019-04-10 23:08:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1565  

http_client_sync.cpp example doesn't compile on the latest boost 1.69.  
  
Error	C1083	Cannot open include file: 'boost/beast/core/tcp_stream.hpp': No such file or directory	  
  
Did beast::tcp_stream move?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-10 23:04:32 UTC  
> Updated at: 2019-04-10 23:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1565#issuecomment-481899828  

If you want to use the examples from **develop** or **master** then you have to use the corresponding version of Beast. You can't compile the latest examples with the older release.  
  
> Did beast::tcp_stream move?  
  
`beast::tcp_stream` is new in Boost 1.70. You can get **rc2** here:  
https://dl.bintray.com/boostorg/release/1.70.0/source/

---

## Comment 2

> Username: csm10495  
> Created at: 2019-04-10 23:08:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1565#issuecomment-481900696  

Ahh. That makes so much sense. Thanks!
