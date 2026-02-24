# #1151 - Parse Shoutcast ICY HTTP responses [Closed]

> Username: tli-invn  
> Created at: 2018-06-07 01:34:10 UTC  
> Updated at: 2018-06-10 14:46:37 UTC  
> Closed at: 2018-06-10 14:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1151  

I am currently using boost::beast to implement a ntrip 1.0 client. It has the request like:  
  
GET /BUCU1 HTTP/1.0 User-Agent: NTRIP GNSSInternetRadio/1.2.0 Authorization: Basic aHVnb2JlbjpodWdvYmVuMTIz  
  
And the response is like: ICY 200 OK  
  
It has the non-standard http response.  
  
I use the beast http client example code, it get this response in the buffer. But it generates a exception in the read function. The error is "Read, Bad version". I am wondering what is the best way to handle the non-standard http response.  
  
```  
void  
    on_write(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
        return fail(ec, "write");  
  
  
    // Receive the HTTP response  
    // boost::beast::flat_buffer buffer_; // (Must persist between reads)  
    // http::response <http::string_body> res_;  
  
    http::async_read(socket_, buffer_, res_,  
        std::bind(  
            &session::on_read,  
            shared_from_this(),  
            std::placeholders::_1,  
            std::placeholders::_2));  
}  
```  
  
void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if (ec)  
        return fail(ec, "read");         // ex  
}

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-07 02:10:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1151#issuecomment-395270063  

See: https://github.com/boostorg/beast/issues/595#issuecomment-334581721

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-07 02:31:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1151#issuecomment-395273103  

Let me spend a little time on this today and see what I can do

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-10 14:46:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1151#issuecomment-396054702  

Please try the **develop** branch which has version 173. The class `icy_stream` may be used to wrap the socket and parse ICY responses:  
```  
boost::beast::icy_stream<boost::asio::ip::tcp::socket> stream(ioc);  
...  
boost::beast::http::response<boost::beast::http::string_body> res;  
beast::http::read(stream, res);  
```
