# #1874 - Concrete types for Body, Allocator and Send [Closed]

> Username: benstadin  
> Created at: 2020-03-12 15:14:00 UTC  
> Updated at: 2020-03-16 20:42:07 UTC  
> Closed at: 2020-03-16 20:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1874  

The examples make use of templates for Body, Allocator and Send inherited from the parser. I'm trying to implement a simple router and need to define concrete types for routing requests to request handler functions.  
  
How can I define concrete types for these?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-12 15:16:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598244890  

Can you show me which code you're talking about? You can just use one of the existing body types such as `http::string_body`.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-12 15:16:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598245134  

Hi @benstadin, thanks for getting in touch.  
  
Are you able to link the example to which you are referring, and perhaps expand on what you want to achieve?  
  
You're welcome to join us in the #beast channel of cpplang slack.  
  
https://cppalliance.org/slack/

---

## Comment 3

> Username: benstadin  
> Created at: 2020-03-12 15:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598258161  

Thanks for your swift reply.   
  
As an example, using the BeastLounge Http Session [1] we have the following method signature for the method handling the requests:  
  
```  
template<  
    class Body, class Allocator,  
    class Send>  
void  
handle_request(  
    beast::string_view doc_root,  
    http::request<Body, http::basic_fields<Allocator>>&& req,  
    Send&& send);  
```  
  
Now I have many of these handler methods in my own application and lots of if..else, and also need to know about some methods in advance for an authorization check upfront. Therefore I want to achieve something like the following pseudo code:  
  
```  
std::map<std::string, std::function<void(string_view, BodyType, SendType)>> operations;  
operations["getSomething"] = std::bind(&HandlerClassA::getHandler<  Body,  Allocator, Send>, _a);  
operations["putSomething"] = std::bind(&HandlerClassA::getHandler<  Body,  Allocator, Send>, _a);  
  
// handle route based on url:  
return operations["talk"](".", body, send);  
```  
  
The problem I have is that the types seem to be implicitly coming from the parser. The parser is defined as:  
  
boost::optional<  
        http::request_parser<  
            http::string_body>> pr_;  
  
Body and Allocator seem implied by parser, and handle_request is called as:  
  
```  
handle_request(  
                srv_.doc_root(),  
                pr_->release(),  
                send_lambda{*this});  
```  
  
[1] https://github.com/vinniefalco/BeastLounge/blob/2b6d5b271e4bf55530d1a16478915f53a855091a/server/http_session.cpp

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-03-12 15:46:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598261362  

You don't have to use `handle_request` or copy its function signature. You can just write your own function which prepares a response and then calls `http::async_write` directly. The point of `handle_request` is to show how the calculation of the HTTP response can be separated from the sending of the response. If you don't care about separating these two concerns, you can just have your handler also perform the write.

---

## Comment 5

> Username: benstadin  
> Created at: 2020-03-12 18:39:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598355108  

That would be an option, though in my actual implementation each handler (I have about 40) calls  a different send method (e.g. a send  method to prepare not found, data responses, authorization failure), depending on the handlers decision. I think I have basically two options: Decoupling handlers and Beast and add an indirection (e.g. map generic return values from handlers to actual send implementations), or using concrete types. The latter would mean less effort.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-03-12 19:41:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1874#issuecomment-598379525  

My vote: Less effort :)
