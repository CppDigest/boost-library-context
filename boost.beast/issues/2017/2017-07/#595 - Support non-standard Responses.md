# #595 - Support non-standard Responses [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 18:17:09 UTC  
> Updated at: 2018-06-10 14:44:42 UTC  
> Closed at: 2018-06-10 14:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/595  

`CURLOPT_HTTP200ALIASES`  
https://curl.haxx.se/libcurl/c/CURLOPT_HTTP200ALIASES.html  
  
Example: IceCast HTTP response:  
```  
ICY 200 OK\r\n"  
...  
```

---

## Comment 1

> Username: djarek  
> Created at: 2017-08-04 06:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-320167786  

I was able to achieve this by adding a ```custom_status_line(string_view line)```protected method to the fields::reader class. Then, the user just has to derive a custom version of fields:  
```C++  
struct event_fields : beast::http::fields {  
    struct reader : beast::http::fields::reader {  
        reader(event_fields const& f,  
        unsigned version, unsigned code) : beast::http::fields::reader{f} {  
            static_cast<void>(version);  
            Expects(code == 200);  
            custom_status_line("EVENT/1.1 200 OK");  
        }  
    };  
};  
```  
This allows the user to use the message just as if it was the beast-provided version:  
```C++  
beast::http::response<beast::http::empty_body, event_fields> response;  
response.result(beast::http::status::ok);  
response.keep_alive(false);  
response.set(beast::http::field::content_type, "application/json");  
std::cout << response << std::endl;  
```  
  
Which prints out as expected:  
```  
EVENT/1.1 200 OK  
Connection: close  
Content-Type: application/json  
  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-04 14:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-320263248  

Wow, that is quite the hack! Very creative. Just one little problem... the intent here is to be able to **parse** such messages not serialize them :)

---

## Comment 3

> Username: djarek  
> Created at: 2017-08-04 18:52:22 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-320326620  

I'm wondering, is there a way of doing this(both parsing custom responses and generating them) without writing a custom version of  the entire Fields class? :/.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-04 19:38:48 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-320336034  

When I wrote the library it was with the goal of implementing HTTP.  
  
`"ICY 200 OK\r\n"` is not HTTP so it is unsurprising that parsing or serializing it is difficult or impossible without changes to the library itself.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-05 20:24:46 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-334581721  

Bjorn brought up the point that there are some niche cases where  
applications do not strictly follow the HTTP message syntax for  
responses. Specifically, for Shoutcast(?) / Icecast mp3 servers the  
status-line in the response looks something like this:  
  
    ICY 200 OK\r\n  
  
I have thought about ways to support this directly in Beast but I have  
decided against it. "ICY 200" is not HTTP according to the RFC, and  
trying to accommodate this use case places needless technical debt in  
public interfaces.  
  
However, I came up with a very simple technique to make this work with  
Beast by writing just a tiny bit of extra code. The solution is to  
implement a stream wrapper which meets the requirements of  
SyncReadStream[1] and/or AsyncReadStream[2] and forwards the calls to  
read to the "next layer" object, which will be the actual socket or  
ssl::stream.  
  
The implementation of the icy_stream wrapper can check to see if the  
first 3 bytes received from the next layer match "ICY" and if so,  
return a buffer to the caller which replaces those characters with  
"HTTP/1.1" (or "HTTP/1.0"). With this wrapper, calls to  
beast::http::read or beast::http::async_read can parse the custom  
response as a regular HTTP message. The wrapper can embed state  
information to inform the caller if the "ICY" string was not received.  
  
The declaration for such a wrapper might look like this:  
  
    template<class NextLayer>  
    class icy_stream;  
  
Usage for the wrapper:  
  
    asio::ip::tcp::socket sock{ios};  
    icy_stream<asio::ip::tcp::socket&> stream{sock};  
    beast::http::response<beast::http::empty_body> res;  
    beast::multi_buffer buffer;  
    beast::http::read(stream, buffer, res);  
  
This technique may of course be generalized to support any desired  
similar but non-standard HTTP requests or responses in a fashion that  
is completely transparent to Beast.  
  
Thanks  
  
1. http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/SyncReadStream.html  
2. http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/AsyncReadStream.html

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:49 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-384003650  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-24 22:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/595#issuecomment-384099876  

This could be provided as an example and tutorial.
