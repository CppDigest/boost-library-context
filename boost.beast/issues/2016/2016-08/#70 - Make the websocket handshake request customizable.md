# #70 - Make the websocket handshake request customizable [Closed]

> Username: z4kn4fein  
> Created at: 2016-08-31 08:58:41 UTC  
> Updated at: 2017-04-15 02:28:01 UTC  
> Closed at: 2016-09-05 15:03:43 UTC  
> Url: https://github.com/boostorg/beast/issues/70  

Currently the websocket handshake request is built up by the lib under the hood, but i need to decorate it with additional header values, please extend the interface with a handshake method which allows the request customization.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-08-31 10:52:52 UTC  
> Updated at: 2017-04-15 02:28:01 UTC  
> Url: https://github.com/boostorg/beast/issues/70#issuecomment-243728950  

How about this:  
  
```  
class set_subprotocols  
{  
    std::string s_;  
  
public:  
    explicit  
    set_subprotocols(std::string s)  
        : s_(s) {}  
  
    template<bool isRequest, class Body, class Headers>  
    void  
    operator()(beast::http::message<isRequest, Body, Headers>& m) const  
    {  
        m.fields.replace("Sec-WebSocket-Protocol", s_);  
    }  
};  
  
using namespace beast::websocket;  
  
stream<boost::ip::tcp::socket> ws;  
...  
ws.set_option(decorate(set_subprotocols{"mqtt"}));  
```  
  
I believe this works in the current version, see:  
http://vinniefalco.github.io/beast/beast/ref/websocket__decorate.html  
  
**NOTE** The `operator()` must be declared as `const` in the latest versions of Beast! (edited)

---

## Comment 2

> Username: z4kn4fein  
> Created at: 2016-09-05 15:03:43 UTC  
> Url: https://github.com/boostorg/beast/issues/70#issuecomment-244767016  

Thx, your solution is working properly, i'm going to close this issue.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-09-05 15:08:38 UTC  
> Url: https://github.com/boostorg/beast/issues/70#issuecomment-244768001  

Glad to hear it!
