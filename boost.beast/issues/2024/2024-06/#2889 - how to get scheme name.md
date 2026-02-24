# #2889 - how to get scheme name [Closed]

> Username: iams13  
> Created at: 2024-06-20 09:57:42 UTC  
> Updated at: 2024-06-21 02:04:31 UTC  
> Closed at: 2024-06-21 02:04:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2889  

http://127.0.0.1:88/test  
sorry I didn't describe it fully, I have use example\advanced\server\advanced_server.cpp  
I want to get the scheme name when the client accesses it  
how to get the string "http://"?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-20 10:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2180296893  

You can use Boost.URL for these purposes: https://www.boost.org/doc/libs/master/libs/url/doc/html/url/overview.html#url.overview.quick_look.accessing

---

## Comment 2

> Username: iams13  
> Created at: 2024-06-20 10:18:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2180323578  

> You can use Boost.URL for these purposes: https://www.boost.org/doc/libs/master/libs/url/doc/html/url/overview.html#url.overview.quick_look.accessing  
  
thanks very much  
sorry I didn't describe it fully, I have use example\advanced\server\advanced_server.cpp  
I want to get the scheme name when the client accesses it

---

## Comment 3

> Username: ashtum  
> Created at: 2024-06-20 10:58:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2180393659  

> thanks very much sorry I didn't describe it fully, I have use example\advanced\server\advanced_server.cpp I want to get the scheme name when the client accesses it  
  
Could you please elaborate on this? In which step do you want to access the scheme? An HTTP request only contains the target.

---

## Comment 4

> Username: iams13  
> Created at: 2024-06-20 11:23:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2180437348  

> Could you please elaborate on this? In which step do you want to access the scheme? An HTTP request only contains the target.  
  
such as "http://127.0.0.1:88/test"  
  
handle_request(  
                beast::string_view doc_root,  
                http::request<Body, http::basic_fields<Allocator>> &req )  
  
req[http::field::host] can obtain "127.0.0.1:88"  
req.target() can obtain "/test"  
I found "protocol" in http::field but is empty  
I want to jump to "https://" after obtaining "http://"

---

## Comment 5

> Username: ashtum  
> Created at: 2024-06-20 11:34:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2180456972  

> such as "http://127.0.0.1:88/test"  
>   
> handle_request( beast::string_view doc_root, http::request<Body, http::basic_fields> &req )  
>   
> req[http::field::host] can obtain "127.0.0.1:88" req.target() can obtain "/test" I found "protocol" in http::field but is empty I want to jump to "https://" after obtaining "http://"  
  
The HTTP request itself doesn't contain the scheme (http/https). The scheme is used by the client to establish the connection but isn't included in the request headers. To determine the scheme on the server side, you typically infer it from the server configuration or the connection context. For example, you can check if the connection uses SSL/TLS to decide if the scheme is "https".   
  
https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages#http_requests

---

## Comment 6

> Username: iams13  
> Created at: 2024-06-21 01:25:50 UTC  
> Updated at: 2024-06-21 01:34:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2889#issuecomment-2181816309  

> > such as "http://127.0.0.1:88/test"  
> > handle_request( beast::string_view doc_root, http::request<Body, http::basic_fields> &req )  
> > req[http::field::host] can obtain "127.0.0.1:88" req.target() can obtain "/test" I found "protocol" in http::field but is empty I want to jump to "https://" after obtaining "http://"  
>   
> The HTTP request itself doesn't contain the scheme (http/https). The scheme is used by the client to establish the connection but isn't included in the request headers. To determine the scheme on the server side, you typically infer it from the server configuration or the connection context. For example, you can check if the connection uses SSL/TLS to decide if the scheme is "https".  
>   
> https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages#http_requests  
  
thank you for your help  
I found out I was wrong before  
I used example\advanced\server-flex\advanced_server_flex.cpp  
Please ignore the bad typography, it seems I don't know how to use it  
The solution is:  
  
in class ssl_http_session  
  
`  
void  
    on_handshake(  
        beast::error_code ec,  
        std::size_t bytes_used)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
  
        // Consume the portion of the buffer used by the handshake  
        buffer_.consume(bytes_used);  
  
        do_read();  
    }  
`  
chenge to:  
`  
void  
    on_handshake(  
                    beast::error_code ec,  
                    std::size_t bytes_used )  
    {  
        if ( ec )  
        {  
            return fail( ec, "handshake" );  
        }  
        // Consume the portion of the buffer used by the handshake  
        buffer_.consume( bytes_used );  
        do_read(true);//ssl tag  
    }  
`  
in class http_session  
`   
void  
    do_read()  
    {  
        // Construct a new parser for each message  
        parser_.emplace();  
  
        // Apply a reasonable limit to the allowed size  
        // of the body in bytes to prevent abuse.  
        parser_->body_limit(boost::none);  
  
        // Set the timeout.  
        beast::get_lowest_layer(  
            derived().stream()).expires_never();//expires_after(std::chrono::seconds(30));  
  
        // Read a request using the parser-oriented interface  
        http::async_read(  
            derived().stream(),  
            buffer_,  
            *parser_,  
            beast::bind_front_handler(  
                &http_session::on_read,  
                derived().shared_from_this()));  
    }  
  
    void  
    on_read(beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return derived().do_eof();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // See if it is a WebSocket Upgrade  
        if(websocket::is_upgrade(parser_->get()))  
        {  
            // Disable the timeout.  
            // The websocket::stream uses its own timeout settings.  
            beast::get_lowest_layer(derived().stream()).expires_never();  
  
            // Create a websocket session, transferring ownership  
            // of both the socket and the HTTP request.  
            return make_websocket_session(  
                derived().release_stream(),  
                parser_->release());  
        }  
  
        // Send the response  
        queue_write(handle_request(*doc_root_, parser_->release()));  
  
        // If we aren't at the queue limit, try to pipeline another request  
        if (response_queue_.size() < queue_limit)  
            do_read();  
    }  
`  
chenge to:  
`   
void  
    do_read(bool bssl = false)  
    {  
        // Construct a new parser for each message  
        parser_.emplace();  
  
        // Apply a reasonable limit to the allowed size  
        // of the body in bytes to prevent abuse.  
        parser_->body_limit(boost::none);  
  
        // Set the timeout.  
        beast::get_lowest_layer(  
            derived().stream()).expires_never();//expires_after(std::chrono::seconds(30));  
  
        // Read a request using the parser-oriented interface  
        http::async_read(  
            derived().stream(),  
            buffer_,  
            *parser_,  
            beast::bind_front_handler(  
                &http_session::on_read,  
                derived().shared_from_this(),  
                bssl));  
    }  
  
    void  
    on_read(bool bssl, beast::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return derived().do_eof();  
  
        if(ec)  
            return fail(ec, "read");  
  
        // See if it is a WebSocket Upgrade  
        if(websocket::is_upgrade(parser_->get()))  
        {  
            // Disable the timeout.  
            // The websocket::stream uses its own timeout settings.  
            beast::get_lowest_layer(derived().stream()).expires_never();  
  
            // Create a websocket session, transferring ownership  
            // of both the socket and the HTTP request.  
            return make_websocket_session(  
                derived().release_stream(),  
                parser_->release());  
        }  
  
        // Send the response  
        queue_write(handle_request(*doc_root_, parser_->release(), bssl));  
  
        // If we aren't at the queue limit, try to pipeline another request  
        if (response_queue_.size() < queue_limit)  
            do_read();  
    }  
`  
Then in handle_request you can determine whether it is ssl
