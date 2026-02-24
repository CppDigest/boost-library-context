# #889 - Websocket client: input sequence, continuous in memory [Closed]

> Username: mstanichenko  
> Created at: 2017-11-15 21:06:02 UTC  
> Updated at: 2017-11-16 12:49:01 UTC  
> Closed at: 2017-11-16 12:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/889  

A simplified version of my websocket-client looks like following  
  
```C++  
boost::beast::multi_buffer b;  
  
for (;;) {  
  ws.read(b);  
  
  // Copy a range of bytes  
  // (asio::buffers_begin(b.data()), asio::buffers_begin(b.data()) + b.size())  
  
  b.consume(b.size())  
}  
```  
  
My question is whether the range that I copy inside `for`-loop above constitutes a  
continuous piece of memory and how a user can access a pointer to it to avoid extra coping inside?  
  
Since I know the maximum size of a websocket message, is there any way to ask `beast` library  
to use a single buffer so that it will be guaranteed that the range above is a continuous piece  
of memory?  
  
Thank you very much for such a great library. This is a great job.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-15 22:28:34 UTC  
> Updated at: 2017-11-15 22:29:07 UTC  
> Url: https://github.com/boostorg/beast/issues/889#issuecomment-344750295  

>is there any way to ask beast library to use a single buffer  
  
Yes, that is very easy. Instead of using `multi_buffer`, use a flat buffer:  
  
Using dynamic allocation:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__flat_buffer.html  
  
Example:  
  
```  
boost::beast::flat_buffer b;  
...  
ws.read(b);  
boost::asio::const_buffer cb = boost::beast::buffers_front(b.data());  
std::cout << boost::beast::string_view(  
    reinterpret_cast<char const*>(cb.data()), cb.size()) << "\n";  
```  
  
Or if you are guaranteed to know the maximum message size you can use a static flat buffer:  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__flat_static_buffer.html  
  
Example:  
  
```  
// Message is guaranteed to be 2,000 bytes or less  
boost::beast::flat_static_buffer<2000> b;  
...  
ws.read(b);  
boost::asio::const_buffer cb = boost::beast::buffers_front(b.data());  
std::cout << boost::beast::string_view(  
    reinterpret_cast<char const*>(cb.data()), cb.size()) << "\n";  
```  
  
Hope this helps!

---

## Comment 2

> Username: mstanichenko  
> Created at: 2017-11-16 12:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/889#issuecomment-344913021  

> Hope this helps!  
  
Definitely! Thank you very much for such a prompt response.  
Your answer covers my questions completely.  
  
Thanks again.
