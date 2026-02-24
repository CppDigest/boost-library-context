# #302 - Failed to decorate websocket handshake request [Closed]

> Username: iamanarchist  
> Created at: 2017-04-11 19:35:25 UTC  
> Updated at: 2017-04-19 15:43:43 UTC  
> Closed at: 2017-04-19 15:43:43 UTC  
> Url: https://github.com/boostorg/beast/issues/302  

The sample code in #70 is not working. Anything changed?  
  
I traced the code a little bit and didn't quite understand how decorator works. The decorator object stored in stream_base is never used. BTW, I used wireshark to debug the actual payload and finally realized the header is missing.  
  
Please help! Thanks :)  
  
```  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
#include <string>  
  
class set_subprotocols  
{  
    std::string s_;  
  
public:  
    explicit  
    set_subprotocols(std::string s)  
        : s_(s) {}  
  
    template<bool isRequest, class Body, class Headers>  
    void  
    operator()(beast::http::message<isRequest, Body, Headers>& m)  
    {  
    	// FIXME this line never got executed  
    	std::cerr << "hahaha" << std::endl;  
        m.fields.replace("Sec-WebSocket-Protocol", s_);  
    }  
};  
  
int main()  
{  
    std::string const host = "127.0.0.1";  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::resolver r{ios};  
    boost::asio::ip::tcp::socket sock{ios};  
    boost::asio::connect(sock,  
        r.resolve(boost::asio::ip::tcp::resolver::query{host, "3123"}));  
  
    beast::websocket::stream<boost::asio::ip::tcp::socket&> ws{sock};  
    // FIXME the decorator is not functional  
    ws.set_option(beast::websocket::decorate(set_subprotocols{"mqtt"}));  
    ws.handshake(host, "/");  
    ws.write(boost::asio::buffer(std::string("Hello, world!")));  
  
    beast::streambuf sb;  
    beast::websocket::opcode op;  
    ws.read(op, sb);  
    ws.close(beast::websocket::close_code::normal);  
    std::cout << beast::to_string(sb.data()) << "\n";  
  
    return 0;  
}  
```

---

## Comment 1

> Username: iamanarchist  
> Created at: 2017-04-11 19:46:00 UTC  
> Url: https://github.com/boostorg/beast/issues/302#issuecomment-293379208  

By the way, if rolling back to 681db2efb8bd31a7b2091574a86ff41f5d5b7f62 the code will work

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-04-13 01:31:23 UTC  
> Url: https://github.com/boostorg/beast/issues/302#issuecomment-293752078  

I am investigating this issue, thanks for the report!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-13 01:56:04 UTC  
> Url: https://github.com/boostorg/beast/issues/302#issuecomment-293755349  

I believe this branch reproduces the defect:  
https://github.com/vinniefalco/Beast/commits/b33  
I'm working on a fix.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-04-15 02:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/302#issuecomment-294266601  

@nctusdk Ah, I figured out the problem! Decorator `operator()` must be declared `const`. Right now Beast uses a too-clever system of metaprogramming to allow one or both of the operators to be excluded, but I think this was a mistake because then the compiler doesn't give you an error if you get the signature wrong. Its so confusing, that even I wrote the wrong code (my unit test decorator members were not declared const!) So something will have to be done!  
  
tl;dr; There is no bug, you need to declare your `operator()` as `const`.  
  
I will change the interface of decorators to generate a compile error if the signatures are wrong.
