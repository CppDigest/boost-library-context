# #738 - Is there any way to cast multi_buffer into std::istream? [Closed]

> Username: tohtsky  
> Created at: 2017-08-14 02:11:28 UTC  
> Updated at: 2017-08-15 04:26:36 UTC  
> Closed at: 2017-08-15 04:26:36 UTC  
> Url: https://github.com/boostorg/beast/issues/738  

First of all, thank you so much for this super-efficient and comfortable library!  
I'm trying to develop an application based on it, which may handle very large JSON or binaries.  
  
My current way of reading JSON using beast::multi_buffer is like  
```  
beast::multi_buffer bf;  
  
/* read ops... */  
  
std::stringstream ss;  
ptree target;  
  
ss << beast::buffers(bf.data());  
boost::property_tree::read_json(ss, target);  
```  
which involves a string copy into ``ss``.  
Is there any way around this?  
Unfortunately it's kind of hard to use library other than ``boost::property_tree`` for handling json...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-14 03:09:14 UTC  
> Url: https://github.com/boostorg/beast/issues/738#issuecomment-322090611  

Are you using HTTP or WebSocket? Or something else?  
  
`boost::asio::basic_streambuf` is derived from `std::streambuf`, perhaps you could use that relationship to construct the `std::istream` to pass to `read_json`?

---

## Comment 2

> Username: tohtsky  
> Created at: 2017-08-15 04:26:36 UTC  
> Url: https://github.com/boostorg/beast/issues/738#issuecomment-322374459  

Thanks for such a quick response! I'm using WebSocket.  
  
> boost::asio::basic_streambuf is derived from std::streambuf  
  
Didn't notice that because I was grepping inside beast source!  
Sorry for my ignorance, and thank you again for this fascinating library.
