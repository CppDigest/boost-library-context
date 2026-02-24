# #2549 - Question: get request headers from beast::websocket::stream? [Closed]

> Username: gunzino  
> Created at: 2022-10-23 12:11:00 UTC  
> Updated at: 2023-10-28 16:39:49 UTC  
> Closed at: 2023-10-28 16:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2549  

Hello,  
  
At first I've used beast::websocket::stream<boost::beast::tcp_stream> based on you're async websocket client/server example. Therefore I came to situation that I need to parse the headers on the server side to get the client IP from CDN. I've solved this by rewritting server to hybrid mode, at first do http::async_read and then upgrade with beast::websocket::stream::async_accept. For this I need to declare 4 new variables which are only used to do the http::async_read and parse the headers:  
```  
    // http  
    boost::beast::tcp_stream stream_;  
    boost::beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    boost::beast::http::request<boost::beast::http::string_body> req_;  
    std::string m_ipHeader;  
  
    // websocket  
    std::optional<boost::beast::websocket::stream<boost::beast::tcp_stream>> ws_;  
```  
Is there a way to somehow read the headers only from the websocket::stream object? My application is pure websocket, http serving is not needed. It's already 3rd issue I create here but you guys seems very helpful and I think it may be helpful for other developers in the future.  
  
I also want to include my server-side http/websocket acceptor code:  
```  
    // first we need to check if http request is with upgrade websocket and get client IP from headers  
    req_ = {};  
    stream_.expires_after(std::chrono::seconds(30));  
    auto self = getThis();  
    boost::beast::http::async_read(stream_, buffer_, req_, [self] (const boost::system::error_code& ec, std::size_t bytes_transferred) {  
        if (ec) {  
            boost::beast::get_lowest_layer(self->stream_).socket().close();  
            return;  
        }  
        if (boost::beast::websocket::is_upgrade(self->req_)) {  
            for (auto& h : self->req_.base()) {  
                if (h.name_string() == "CF-Connecting-IP") { // cloudflare  
                    self->m_ipHeader = std::string(h.value());  
                    break;  
                } else if (h.name_string() == "X-Real-IP") { // Gcore  
                    self->m_ipHeader = std::string(h.value());  
                    break;  
                } else if (h.name_string() == "Incap-Client-IP") { // imperva  
                    self->m_ipHeader = std::string(h.value());  
                    break;  
                } else if (h.name_string() == "X-Forwarded-For") { // google/amazon  
                    if (h.value().find(",")) { // google send 2 IPs  
                        self->m_ipHeader = std::string(h.value()).substr(0, h.value().find(","));  
                    } else {  
                        self->m_ipHeader = std::string(h.value());  
                    }  
                }  
            }  
            boost::system::error_code ecc;  
            self->m_ip = boost::asio::ip::address_v4::from_string(self->m_ipHeader, ecc).to_ulong();  
            if (ecc) {  
                self->m_ip = boost::beast::get_lowest_layer(self->stream_).socket().remote_endpoint(ecc).address().to_v4().to_ulong();  
            }  
            self->ws_.emplace(self->stream_.release_socket());  
            self->ws_->set_option(  
                boost::beast::websocket::stream_base::timeout::suggested(  
                        boost::beast::role_type::server));  
  
            // Set a decorator to change the Server of the handshake  
            self->ws_->set_option(boost::beast::websocket::stream_base::decorator(  
                [](boost::beast::websocket::response_type& res)  
                {  
                    res.set(boost::beast::http::field::server,  
                            std::string("Boost.Beast/300 Server"));  
                }));  
            boost::beast::get_lowest_layer(*self->ws_).socket().set_option(boost::asio::ip::tcp::no_delay(true), ecc);  
            boost::beast::websocket::permessage_deflate opt;  
            opt.server_enable = false;  
            self->ws_->set_option(opt);  
  
            self->ws_->async_accept(self->req_, [self](const boost::system::error_code& ec) {  
                if (ec) {  
                    return self->terminate();  
                }  
                // START BI-DIRECTIONAL COMMUNICATION HERE  
            });  
            return;  
        } else {  
            boost::beast::get_lowest_layer(self->stream_).socket().close();  
        }  
    });  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-23 12:33:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1288102701  

You can capture when you perform the handshake (on the client side) with the second overload as shown here:  
  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload2.html

---

## Comment 2

> Username: gunzino  
> Created at: 2022-10-23 12:47:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1288105228  

@klemens-morgenstern   
I am trying to achieve it in server-side. Is there serverside solution too?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-23 19:44:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1288185463  

Ah sorry, got it now. This is not a  "hybrid mode", a websocket is just a very long http request. So the above code is the way to do this.  
  
The websocket doesn't keep the headers around.  
  
If your issue is that the code doesn't look nice with the callback hell, you might want to look into asio::async_compose. See here for an example: https://cppalliance.org/richard/2020/04/30/RichardsAprilUpdate.html

---

## Comment 4

> Username: gunzino  
> Created at: 2022-10-24 19:33:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1289501907  

@klemens-morgenstern   
  
What about limiting server to be able to response to GET requests only? Just to prevent abusing of POST/PUT requests with big body size. Does Beast limit maximum body size? Does it load body to memory even if requested method is GET?

---

## Comment 5

> Username: ecorm  
> Created at: 2023-08-10 04:49:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1672548806  

I also need access to the headers server-side in a pure websockets application, in particular the `Sec-WebSocket-Protocol` field.

---

## Comment 6

> Username: ecorm  
> Created at: 2023-08-11 21:06:23 UTC  
> Updated at: 2023-08-11 21:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1675402728  

Just wanted to report that the workaround suggested by gunzino works for me. Also, manually parsing the HTTP upgrade request is covered in [the documentation](https://www.boost.org/doc/libs/release/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests).  
  
It would be nice to have an `websocket::stream::async_accept` overload that takes an extra parameter for a user-supplied callback for inspecting the upgrade request and possibly rejecting it.

---

## Comment 7

> Username: ashtum  
> Created at: 2023-08-18 08:53:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2549#issuecomment-1683585864  

> @klemens-morgenstern  
>   
> What about limiting server to be able to response to GET requests only? Just to prevent abusing of POST/PUT requests with big body size. Does Beast limit maximum body size? Does it load body to memory even if requested method is GET?  
  
You can set a limit on the buffer you are using with [http::async_read](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload2.html) by, for example, passing a limit to the [flat_buffer](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__basic_flat_buffer/basic_flat_buffer/overload2.html) constructor.  
Another option would be to instantiate an http::parser and set the [body_limit](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/body_limit.html) on it, and then use the [http::async_read overload](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html) that accepts an http::parser.
