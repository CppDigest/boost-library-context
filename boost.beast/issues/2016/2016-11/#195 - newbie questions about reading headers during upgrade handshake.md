# #195 - newbie questions about reading headers during upgrade handshake [Closed]

> Username: vchang-akamai  
> Created at: 2016-11-19 00:25:19 UTC  
> Updated at: 2017-05-08 00:04:00 UTC  
> Closed at: 2017-05-08 00:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/195  

I'm evaluating Beast for possible usage in an upcoming websocket server project.  
  
We need to examine the incoming request headers during the websocket upgrade handshaking.  I found some docs about handshaking: [http://vinniefalco.github.io/beast/beast/websocket/handshaking.html](http://vinniefalco.github.io/beast/beast/websocket/handshaking.html)  I still have some newbie questions to confirm my understanding...  
  
On the server side, what does websocket::stream::accept() (and async_accept()) check to determine that the upgrade is ok? Is it just a bare minimum of request headers "Upgrade: websocket", "Connection: Upgrade", "Sec-WebSocket-Key", and "Sec-WebSocket-Version"? Does it also check "Origin"?  
  
If my server needs to verify supported values for "Sec-WebSocket-Protocol" or "Sec-WebSocket-Extensions" before accepting, does that mean I need to use beast::http::read()/async_read() first to get the request headers and check them manually?  Does Beast have any convenience APIs for accessing these websocket-related headers?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-19 17:37:26 UTC  
> Updated at: 2016-11-19 17:41:11 UTC  
> Url: https://github.com/boostorg/beast/issues/195#issuecomment-261727703  

(Related to https://github.com/vinniefalco/Beast/issues/80)  
  
Yes, this idea is that you use `beast::http::read` or `beast::http::async_read` to get the request, and then do any preprocessing you want on it. But thinking about your use case, I guess you would want a function like this (note: untested):  
  
```  
#include <beast/http/message.hpp>  
#include <beast/http/rfc7230.hpp>  
  
namespace beast {  
namespace websocket {  
  
/// Returns `true` if the request indicates a WebSocket upgrade  
bool is_upgrade(beast::http::request_header const& req)  
{  
    if(req.method != "GET")  
        return false;  
    if(req.version < 11)  
        return false;  
    if(! beast::http::is_upgrade(req))  
        return false;  
    if(! beast::http::token_list{req.fields["Upgrade"]}.exists("websocket"))  
        return false;  
    if( req.fields["Sec-WebSocket-Version"] != "13")  
        return false;  
    return true;  
}  
  
} // websocket  
} // beast  
```  
  
You can use the overload of `beast::websocket::stream::accept` or `beast::websocket::stream::async_accept` which lets you pass the request in:  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/accept/overload5.html  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_accept/overload3.html

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2016-11-30 00:36:52 UTC  
> Updated at: 2016-11-30 01:02:01 UTC  
> Url: https://github.com/boostorg/beast/issues/195#issuecomment-263746647  

I started skimming through some of the source code. I see that stream&lt;NextLayer&gt;::build_response()  (in websocket/impl/stream.ipp) performs the request headers checking of the upgrade handshake for websocket::stream::accept().  
  
build_response() constructs the handshake response.  What if I need to add "Sec-WebSocket-Protocol" or "Sec-WebSocket-Extensions" in the response?  I see there is websocket::decorate()...  
  
Would something like this work? (uncompiled code mostly borrowed from your docs examples :-)  
  
  
```  
struct response_subprotocol_decorator  
{  
    std::string subprotocol_;  
  
    response_subprotocol_decorator(std::string subprotocol) :  
        subprotocol_(subprotocol)  
    {}  
  
    template<bool isRequest, class Body, class Fields>  
    void  
    operator()(http::message<isRequest, Body, Fields>& m)  
    {  
        if(!isRequest)  
        {  
            if (m.status == 101 && m.fields["Upgrade"] == "websocket")  
                m.fields.replace("Sec-WebSocket-Protocol", subprotocol_);  
        }  
    }  
};  
  
[...]  
  
void do_accept(boost::asio::ip::tcp::socket& sock)  
{  
    boost::asio::streambuf sb;  
    beast::http::request<http::empty_body> request;  
    beast::http::read(sock, sb, request);  
  
    if(beast::http::is_upgrade(request))  
    {  
        std::string response_subprotocol("default_proto");  
  
        /* TO DO HERE:  
            Look for "Sec-WebSocket-Protocol" in request headers.  
            Set response_subprotocol to the preferred subprotocol (if any)  
            based on whatever app-specific rules...  
         */  
  
        websocket::stream<ip::tcp::socket&> ws{sock};  
        ws.set_option(decorate(response_subprotocol_decorator{response_subprotocol}));  
        ws.accept(request);  
        [...]  
    }  
}  
  
```  
  
In this example, I didn't use your beast::websocket::is_upgrade(). Instead, I let accept() (calling build_response()) check the handshake headers. If it passes the header checks, I can decorate the resulting 101 websocket upgrade response.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-30 02:16:14 UTC  
> Url: https://github.com/boostorg/beast/issues/195#issuecomment-263762890  

Yes, the "decorator" is the recommended way of customizing the handshake. I should probably provide a function like this:  
```  
/// Returns `true` if `msg` is a WebSocket Upgrade request.  
template<class Fields, class Body>  
bool  
is_websocket_upgrade(message<true, Fields, Body> const& msg);  
```
