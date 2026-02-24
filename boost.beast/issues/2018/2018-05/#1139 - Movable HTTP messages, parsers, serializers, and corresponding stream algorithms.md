# #1139 - Movable HTTP messages, parsers, serializers, and corresponding stream algorithms [Open]

> Username: rinick  
> Created at: 2018-05-22 22:14:46 UTC  
> Updated at: 2022-09-25 00:24:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1139  

beast use a lot of L value ref as parameter, sometimes this makes it really painful to design my class  
  
for example:  
  
`http:async_write` takes either a `message<isRequest, Body, Fields>&` or `serializer<isRequest, Body, Fields>&`, which means something else need to hold the value until the callback is done.  
  
but very often I generated this message or serializer in function body that doesn't own any data structure to store it.  this force me to redesign the class, many lines of code are added to make sure messages are kept in memory until callback is called   
  
if functions like `async_write` can have a version to accept a R value ref and store it internally. my class would be much simpler and easy to read

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-22 23:03:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-391168375  

I'll see what I can do!

---

## Comment 2

> Username: daandemeyer  
> Created at: 2018-07-04 17:39:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-402532736  

I've been going over the Beast async http server example and it definitely has to jump through some hoops because of this.   
  
```cpp  
 template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg) const  
        {  
            // The lifetime of the message has to extend  
            // for the duration of the async operation so  
            // we use a shared_ptr to manage it.  
            auto sp = std::make_shared<  
                http::message<isRequest, Body, Fields>>(std::move(msg));  
  
            // Store a type-erased version of the shared  
            // pointer in the class to keep it alive.  
            self_.res_ = sp;  
  
            // Write the response  
            http::async_write(  
                self_.socket_,  
                *sp,  
                boost::asio::bind_executor(  
                    self_.strand_,  
                    std::bind(  
                        &session::on_write,  
                        self_.shared_from_this(),  
                        std::placeholders::_1,  
                        std::placeholders::_2,  
                        sp->need_eof())));  
        }  
```  
  
This is the biggest offender I could find in the example. The response in the example has to be stored in a type erased shared pointer in the class to make sure it outlives the `async_write` call. This is not intuitive for first time users. If we could move the response into `async_write` the type erased shared pointer wouldn't be necessary and this code would become much cleaner.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-04 18:08:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-402536690  

The overload of `async_write` which accepts a message is really just a toy function anyway. Do you suggest simply adding an overload, e.g. for `message&&`?

---

## Comment 4

> Username: daandemeyer  
> Created at: 2018-07-04 19:13:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-402545448  

Yup, I think that's all that's necessary to be able to write the above example like this:  
  
```cpp  
template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg) const  
        {  
            http::async_write(  
                self_.socket_,  
                std::move(msg),  
                boost::asio::bind_executor(  
                    self_.strand_,  
                    std::bind(  
                        &session::on_write,  
                        self_.shared_from_this(),  
                        std::placeholders::_1,  
                        std::placeholders::_2,  
                        sp->need_eof())));  
        }  
```  
  
which I think is a lot easier to understand.

---

## Comment 5

> Username: daandemeyer  
> Created at: 2018-07-13 17:46:19 UTC  
> Updated at: 2018-07-13 18:47:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-404904783  

After working with Beast more I think it would be incredibly useful to not only be able to move responses into `async_write`, but be able to move all messages/parsers/serializers into the Beast I/O functions. To allow them to be used/inspected after the async I/O is done they could be passed as an argument to the completion handler when done. In my opinion, this creates a very natural flow of a message/parser/serializer moving through the I/O functions and callbacks without the user having to resort to shared pointers to keep the messages/parsers/serializers alive.  
  
The argument could be made that buffers and streams are also passed by reference and need to be kept alive and as such the same can be done with requests/parsers/serializers. The difference I've noticed is that requests/parsers/serializers are very often templated where we want functions/handlers to work with multiple types of bodies. Because they are templated but still need to be kept alive we see solutions such as the shared_ptr<void> to be able to keep any kind of parser/serializer alive regardless of its body type. This isn't really necessary with buffers/streams since we know the exact type and can just store it in our class with no need for shared pointers. Using the shared_ptr<void> method also doesn't work if you need to access the message/parser/serializer again in the completion handler. I've been getting around this by capturing a shared pointer to my parser in a lambda instead before calling the I/O function.   
  
I think being able to move messages/parsers/serializers into I/O functions and getting them back in the completion handler allows for a very intuitive flow with less workarounds to keep requests/parsers/serializers alive while they are being used by I/O functions (instead this responsibility is moved to the I/O function itself which keeps it alive and returns it back to the user in the completion handler).  
  
The example would then look like this:  
```cpp  
template<bool isRequest, class Body, class Fields>  
        void  
        operator()(http::message<isRequest, Body, Fields>&& msg) const  
        {  
            http::async_write(  
                self_.socket_,  
                std::move(msg),  
                boost::asio::bind_executor(  
                    self_.strand_,  
                    std::bind(  
                        &session::on_write<isRequest, Body, Fields>,  
                        self_.shared_from_this(),  
                        std::placeholders::_1,  
                        std::placeholders::_2,  
                        std::placeholders::_3,  
                        sp->need_eof())));  
        }  
  
...  
template <bool isRequest, class Body, class Fields>  
session::on_write(http::message<isRequest, Body, Fields> &&msg, boost::beast::error_code ec, std::size_t bytes_transferred) {  
...  
}  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-07-13 18:00:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-404908567  

Yeah...if only it was so easy :) I agree with almost everything you wrote. But there are significant design issues which need to be addressed.  
  
* Stackful coroutines work by passing a special completion token, for example `basic_yield_context yield`. The 2nd parameter to the completion handler becomes the return value of the asynchronous operation. If you to capture the error, you apply the array index operator to the token, e.g. `yield[ec]`. But when the completion handler has 3 parameters, as in your example above, things are more complicated. `basic_yield_context` does not have a return type specialization for completion handlers with 3 arguments, so Asio (and by extension, networking-ts) would need to be changed to accommodate it.  
  
* Right now, not all messages are **MoveConstructible**. It is possible to have **Fields** and `Body::value_type` implementations which are not movable. Furthermore, `Body::writer` is not guaranteed to be movable. This makes parsers and serializers not movable. To make things even more interesting, there is a problem when passing buffers referencing a `basic_string`. If the std implementation uses the small string optimization (and almost all of them do) then moving the string would invalidate the buffer if the string is used in an asynchronous operation.  
  
* The message oriented version of async read and write is really just a toy function for the examples. I am reluctant to expand on its functionality. In fact, the proposal for HTTP in the standard library might just omit those functions, since they have significant limitations. Callers are locked to the defaults, and setting timeouts is tricky (what if you send a 2GB message for example).  
  
Note that you don't need a `shared_ptr`, you can get away with a `unique_ptr` to stash the parser or serializer, because Asio now only requires handlers to be movable, it avoids making copies. So you could roll your own abstraction that supports taking ownership of the serializer and parser in a unique_ptr and calls your completion handler with your extended arguments.  
  
Either way, more thought and design is needed to resolve these points.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-01-18 15:24:46 UTC  
> Updated at: 2020-01-18 15:26:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-575908269  

I'm thinking about going in the opposite direction. For the parser, we build the necessary dynamic buffer into it (which would become an implementation detail). This would be passed by reference. This parser would not be tied to a particular body type. Instead, at the call site of the initiating function or synchronous operation, you specify the body that you want.  
  
Here's a rough sketch:  
```  
http::async_read_body(  
    sock_,  
    parser_,  
    http::string_body(),  
    [&]( expected<std::string> body )  
    {  
        ...  
    });  
```  
  
Note how the body is passed, instead of being in the message. You could combine them later if you want, but with this API the HTTP headers do not need to be copied. Instead, you access them through the parser. And this lets the parser be independent of the body, thus it can be part of the "connection" object and share its lifetime.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-06-27 21:30:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-650632152  

Have a look at these:  
  
https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_generator.hpp  
https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/buffers_generator.hpp#L128  
  
Usage:  
  
https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_service.cpp#L157  
https://github.com/vinniefalco/BeastLounge/blob/44194d746fdb37376ddd998b888389eff7c27ef7/src/http_connection.cpp#L147

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:17:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-1256877725  

Can this be closed with the merge of #2421.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-09-25 00:24:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1139#issuecomment-1257089603  

I think this should be closed. My new design for HTTP requires a persistent parser and a persistent serializer. These are not templates anymore, and the new message model is not templated either. I think a lot of the pain and the motivation for the original issue is because of the various body types, and that the parser and serializer are one-shot. The right fix is a new design not to layer more bandages on top of something that is fundamentally flawed.
