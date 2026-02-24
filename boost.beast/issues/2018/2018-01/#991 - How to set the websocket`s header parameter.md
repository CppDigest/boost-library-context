# #991 - How to set the websocket`s header parameter? [Closed]

> Username: lls2wow  
> Created at: 2018-01-20 08:05:43 UTC  
> Updated at: 2018-04-27 00:21:01 UTC  
> Closed at: 2018-04-26 20:30:12 UTC  
> Url: https://github.com/boostorg/beast/issues/991  

Hi @vinniefalco ,  
  
      Would you kind tell me how to set  the websocket`s header parameter?  
  
      Like 'headers.put("User-Agent", "Mozilla/5.0 (iPhone; CPU iPhon….0 Mobile/14E304 Safari/602.1");'

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-20 12:01:19 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-359166497  

You want a "request decorator." This is explained here:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.decorators

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-01-26 16:28:26 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-360833123  

Has this issue been resolved?

---

## Comment 3

> Username: lls2wow  
> Created at: 2018-02-01 09:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-362211754  

Yes, Thank you it works well ,sorry to respond lately.@vinniefalco

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-01 12:35:46 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-362253128  

No problem at all, glad it worked for you! I'll go ahead and close this issue now. Feel free to open new ones if you need to.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-26 16:14:28 UTC  
> Updated at: 2018-04-26 16:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-384700504  

Maybe you could please post the output of the compiler which shows the error?

---

## Comment 6

> Username: bernardlawes  
> Created at: 2018-04-26 20:37:32 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-384780986  

Hi Vinne Falco, sorry if the rudimentary question..   But I am a noob on this.  
  
In your example for using decorators:   
**ws.handshake_ex("localhost", "/",  
    [](request_type& m)  
    {  
        m.insert(http::field::sec_websocket_protocol, "xmpp;ws-chat");  
    });**  
  
I am receiving the errors during compile:  
(1) syntax error: identifier 'request_type'  
(2) 'req' cannot be implicitly captured because no default capture mode has been specified  
  
Do you have a full example of connecting to a websocket after having modified the websocket header parameter?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-26 20:43:21 UTC  
> Updated at: 2018-04-26 20:43:30 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-384782703  

You need to use the correct namespace to access the type: `boost::beast::websocket::request_type`. This is explained in the documentation, and you can see it in the header:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/stream.hpp#L45  
  
The documentation explains:  
_"...Sample code and identifiers appearing in this section is written as if these declarations are in effect:"_  
```  
#include <boost/beast/websocket.hpp>  
using namespace boost::beast::websocket;  
```  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket.html

---

## Comment 8

> Username: bernardlawes  
> Created at: 2018-04-26 21:01:11 UTC  
> Updated at: 2018-04-27 00:21:01 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-384787634  

Wow! your responsiveness is without reproach.  Thank you, it works!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-04-26 22:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/991#issuecomment-384807288  

"RequestDecorator requirements not met" means that your _lambda-expression_ does not have the correct signature, which is `void(boost::beast::websocket::request_type)`.  
  
>I want to also include and outside outside parameter in the handshake_ex lambda expression.  
  
Okay, so this is not really a Beast question but more of a C++ question.  If you want your lambda to have access to variables outside of its scope, you need to include them in the _capture-expression_ (the stuff inside the square brackets). For example if `headers` is a local variable then you might write:  
  
```  
	ws.handshake_ex(wsHost, "/",  
		[&headers](request_type& req)  
	{	  
		for (auto const &header : headers)  
			req.set(header.first, header.second);  
	});  
```  
  
You can read more about lambda capture expressions here:  
http://en.cppreference.com/w/cpp/language/lambda#Lambda_capture  
https://stackoverflow.com/questions/7627098/what-is-a-lambda-expression-in-c11
