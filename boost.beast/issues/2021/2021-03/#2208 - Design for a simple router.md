# #2208 - Design for a simple router [Closed]

> Username: Tectu  
> Created at: 2021-03-26 10:22:11 UTC  
> Updated at: 2021-03-28 14:27:42 UTC  
> Closed at: 2021-03-28 14:27:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2208  

I'm currently attempting writing a small, simple HTTP router on top of beast and I would like to get some external opinions on the design of this.  
  
# Overview  
The usage of this router should look like this:  
```cpp  
using string_response = boost::beast::http::response<boost::beast::http::string_body>;  
  
router->add(verb::post, "/some/endpoint", [](const auto& req) {  
    string_response res{boost::beast::http::status::ok, req.version()};  
    // ...  
    return res;  
});  
  
router->add(verb::get, "/some/endpoint", [](const auto& req) {  
    string_response res{boost::beast::http::status::ok, req.version()};  
    // ...  
    return res;  
});  
```  
The `router::add()` function would create an instance of `route` and store it in some sort of collection/container.  
The `route` might look like this:  
```cpp  
template<typename OnRequest>  
struct route  
{  
    boost::beast::http::verb verb;  
    std::regex rule;  
    OnRequest handler;  
};  
```  
The `router` could be something along the lines of:  
```cpp  
struct router  
{  
    using verb_type  = boost::beast::http::verb;  
    using request_type  = boost::beast::http::message<true,  boost::beast::http::string_body, boost::beast::http::basic_fields<std::allocator<char>>>;  
    using response_type = boost::beast::http::message<false, boost::beast::http::string_body, boost::beast::http::basic_fields<std::allocator<char>>>;  
    using handler_type  = std::function<response_type(const request_type&)>;  
    using route_type = route<handler_type>;  
  
    /**  
     * Add a route  
     */  
    void add_route(const verb_type& verb, const std::string_view target, handler_type&& handler)  
    {  
        route_type r;  
        r.verb = verb;  
        r.rule = std::regex{ target.cbegin(), target.cend() };  
        r.handler = std::move(handler);  
  
        m_routes.emplace_back(std::move(r));  
    }  
  
    /**  
     * Handles an incoming request. This gets called by the session.  
     */  
    template<  
        class Body, class Allocator,  
        class Send  
    >  
    void handle_request(  
        beast::string_view doc_root,  
        boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator>>&& req,  
        Send&& send  
    )  
    {  
        // ...  
    }  
private:  
    std::vector<route_type> m_routes;  
};  
```  
The `session` is based on the official beast example [/example/advanced/server](https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp). The `session::on_read()` function would call `router::handle_request()`. Something like this:  
```cpp  
void session::on_read(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
    // This means they closed the connection  
    if (ec == boost::beast::http::error::end_of_stream)  
        return do_close();  
  
    // Check for errors  
    if (ec) {  
        // ...  
        return;  
    }  
  
    // Hand off to router  
    m_router->handle_request(*m_doc_root, m_parser->release(), m_queue);  
}  
```  
  
# Questions  
My main concern is regarding the handling of handlers that return different `beast::http::message<false, Body, Fields>` types. Not every handler is going to return a `string_body`. Some might return a `file_body` or any other valid `beast::http::message` type.  
  
What is a good/suitable way to extend this code so that a route can return a different `beast::http::message`?  
  
Templating the return type of `router::handler_type` would mean that the `router::m_routes` vector would need to store routes of different types - which does not work.  
  
Templating the `router` itself also doesn't work as again: Different routes will return different types of `beast::http::message`.  
  
A polymorphic approach will not work either as `beast::http::message` itself is templated.  
  
One solution I can think of is not having the `route::OnRequest` function returning the message but instead providing a second parameter that can be used to send a response directly within the handler. This would mean that I somehow have to pass the `queue::operator()` to the handler. However, this also seems to lead to issues regarding different `route`s having different types.  
  
Could somebody provide some information/guideline/recommendation/advice on this?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-26 10:38:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2208#issuecomment-808109318  

> A polymorphic approach will not work either as beast::http::message itself is templated.  
  
Perhaps use the techniques described in Sean Parent's talk on "inheritance is an implementation detail" ?  
  
https://www.youtube.com/watch?v=2bLkxj6EVoM

---

## Comment 2

> Username: Tectu  
> Created at: 2021-03-26 11:08:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2208#issuecomment-808126746  

Thank you for sharing that talk - I'll certainly rewatch that a 2nd time.  
  
Do you think that it would be possible for you to roughly outline how to apply that paradigm in this case without stretching your good-will?

---

## Comment 3

> Username: kervinpierre  
> Created at: 2021-03-26 14:03:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2208#issuecomment-808245197  

As far as router design goes, I'd definitely lean heavily on ExpressJS: https://expressjs.com/  
  
Express has an async API that has been ported to multiple languages.  
  
There was an effort at:  https://github.com/expresscpp/expresscpp

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-26 19:56:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2208#issuecomment-808475770  

I have some experimental code which type-erases a message so you can return it from a function and send it later:  
https://github.com/vinniefalco/BeastLounge/blob/066147b24abe38b2d0e6c80d6fc5d6239e6c4b77/src/http_generator.hpp#L22  
https://github.com/vinniefalco/BeastLounge/blob/066147b24abe38b2d0e6c80d6fc5d6239e6c4b77/src/buffers_generator.hpp#L128  
  
Usage:  
https://github.com/vinniefalco/BeastLounge/blob/066147b24abe38b2d0e6c80d6fc5d6239e6c4b77/src/http_service.cpp#L158  
  
If you have questions feel free to post them here
