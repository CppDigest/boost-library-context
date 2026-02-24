# #2715 - co_await http::async_read does not wait for full http request in advanced_server_flex_awaitable.cpp [Closed]

> Username: DF9IR  
> Created at: 2023-08-10 13:22:14 UTC  
> Updated at: 2023-08-18 03:40:21 UTC  
> Closed at: 2023-08-18 03:40:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2715  

Hi Guys,  
first of all, Boost.Beast is really awesome and it's the reason I learned Boost.Asio in the last month (which is awesome too!)  
  
I tought I made some mistake while trying to implement some web api stuff using the advanced_server_flex_awaitable.cpp example as a template. But after being unable to find any mistake I tried the original example and I can observe the same issue.  
  
In short: the  
`http::async_read(stream, buffer, parser, net::deferred),`  
in line 994 seems to return immediately (I also tried to co_await it in a loop) even if the client / browser does not send anything. The received bytes is 0 and there is no error.  
  
If I access the server with a browser (I'm using firefox) this results in the loop runnig a few times repeating the reply to the browser until the browser cuts the connection. If I do a http request manually (e.g. with putty) then I also get the http response infinitely until I close the connection myself.  
  
Of course the example still kind of works, but is that the intended behaviour? I would expect that:  
  
`co_await http::async_read(stream, buffer, parser, net::use_awaitable);`  
  
would suspend the coroutine until the browser sends another http request?  
  
Btw: The example also contains a lot of code / classes which seems not to belong to the coroutine example. (maybe they where copied there for lookup while writing the example?)  
  
### Version of Beast  
BOOST_BEAST_VERSION 347  
(Boost 1.82.0)  
  
### Steps necessary to reproduce the problem  
  
Runing the advanced server flex awaitable on windows 10:  
  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex-awaitable/advanced_server_flex_awaitable.cpp  
  
### All relevant compiler information  
Windows 10  
GCC 11.2 64 bit which ships with Qt 6.5.2 (Mingw 11.2)  
GCC 13.2 mysys latest (tried today)  
CMake

---

## Comment 1

> Username: DF9IR  
> Created at: 2023-08-11 09:22:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2715#issuecomment-1674447097  

Some more extra info:  
  
- I forgot to mention that I run the program only with one thread (on port 8080)  
- I also tested it on debian and observed the same behaviour:  
`6.1.0-11-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.38-3 (2023-08-07) x86_64 GNU/Linux`  
`gcc (Debian 12.2.0-14) 12.2.0`  
`boost 1.82.0`

---

## Comment 2

> Username: DF9IR  
> Created at: 2023-08-16 12:54:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2715#issuecomment-1680551694  

I think I solved it!  
  
In the documentation for request_parser on the bottom (Remarks):  
> A new instance of the parser is required for each message.   
  
(https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__request_parser.html#beast.ref.boost__beast__http__request_parser.remarks)  
  
But this was not the case in the example and my code which I build upon the example stuff. If the `http::request_parser<http::string_body>` parser; from line 947 is being created newly for every loop iteration it works normally.  
  
https://github.com/boostorg/beast/blob/b0f6f9c39c9f281920808484d8327468f699d6d8/example/advanced/server-flex-awaitable/advanced_server_flex_awaitable.cpp#L947  
  
I solved this (probably horribly with heap memory) that way for making the code work immediately (as example):  
  
  
``` cpp  
template<typename Stream>  
BOOST_ASIO_NODISCARD net::awaitable<void, executor_type>  
run_session(Stream & stream, beast::flat_buffer & buffer, const std::shared_ptr<std::string const> & doc_root)  
{  
    auto parser = std::make_unique<http::request_parser<http::string_body>>();  
    // Apply a reasonable limit to the allowed size  
    // of the body in bytes to prevent abuse.  
    parser->body_limit(10000);  
  
    auto [ec, bytes_transferred] = co_await http::async_read(stream, buffer, *parser);  
  
    if(ec == http::error::end_of_stream)  
        co_await do_eof(stream);  
  
    if(ec)  
        co_return fail(ec, "read");  
  
    // this can be  
    // while ((co_await net::this_coro::cancellation_state).cancelled() == net::cancellation_type::none)  
    // on most compilers  
    for (auto cs = co_await net::this_coro::cancellation_state;  
         cs.cancelled() == net::cancellation_type::none;  
         cs = co_await net::this_coro::cancellation_state)  
    {  
        if(websocket::is_upgrade(parser->get()))  
        {  
            // Disable the timeout.  
            // The websocket::stream uses its own timeout settings.  
            beast::get_lowest_layer(stream).expires_never();  
  
            co_await run_websocket_session(stream, buffer, parser->release(), doc_root);  
            co_return ;  
        }  
  
        // we follow a different strategy then the other example: instead of queue responses,  
        // we always to one read & write in parallel.  
  
        auto res = handle_request(*doc_root, parser->release());  
        parser = std::make_unique<http::request_parser<http::string_body>>();  
        parser->body_limit(10000);  
  
        if (!res.keep_alive())  
        {  
            http::message_generator msg{std::move(res)};  
            auto [ec, sz] = co_await beast::async_write(stream, std::move(msg));  
            if (ec)  
                fail(ec, "write");  
            co_return ;  
        }  
  
        http::message_generator msg{std::move(res)};  
  
        auto [_, ec_r, sz_r, ec_w, sz_w ] =  
                co_await net::experimental::make_parallel_group(  
                    http::async_read(stream, buffer, *parser, net::deferred),  
                    beast::async_write(stream, std::move(msg), net::deferred))  
                        .async_wait(net::experimental::wait_for_all(),  
                                    net::as_tuple(net::use_awaitable_t<executor_type>{}));  
  
        if (ec_r)  
            co_return fail(ec_r, "read");  
  
        if (ec_w)  
            co_return fail(ec_w, "write");  
    }  
}  
```  
  
  
I noticed that this example worked:  
https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp  
  
and slowly slowly compared what was different. This example uses `http::request<http::string_body>` instead of request_parser but the request is also "fresh" every iteration.  
  
I hope this is helpful. Should I close this ticket now?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-16 14:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2715#issuecomment-1680746952  

Let me fix it first, I'll close when the fix is in develop. Thank you very much for looking into it.
