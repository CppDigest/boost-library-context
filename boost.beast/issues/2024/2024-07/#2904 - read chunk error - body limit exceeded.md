# #2904 - read chunk error : body limit exceeded [Closed]

> Username: xiaoma565  
> Created at: 2024-07-18 15:44:48 UTC  
> Updated at: 2024-07-20 10:09:41 UTC  
> Closed at: 2024-07-20 10:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2904  

bro, I got a error "body limit exceeded" when I am reading chunk response.  
I did't set `body_limit()`. Here is my partial code:  
```c++  
void ClientConnection::Start(uint32_t id, HttpClient *client)  
{  
    auto self(shared_from_this());  
    id_ = id;  
    client_ = client;  
    headerCb = std::bind(&ClientConnection::ParserChunkHeader, self, std::placeholders::_1,  
        std::placeholders::_2, std::placeholders::_3);  
    bodyCb = std::bind(&ClientConnection::ParserChunkBody, self, std::placeholders::_1,  
        std::placeholders::_2, std::placeholders::_3);  
    DoReadHeader();  
}  
  
void ClientConnection::DoReadHeader()  
{  
    auto self(shared_from_this());  
    p_.emplace();  
    if (timeout_ > 0) {  
        stream_.expires_after(std::chrono::seconds(timeout_));  
    } else {  
        stream_.expires_at(boost::asio::steady_timer::time_point::max());  
    }  
    boost::beast::http::async_read_header(stream_, buffer_, *p_,  
        boost::beast::bind_front_handler(&ClientConnection::OnReadHeader, self));  
}  
  
void ClientConnection::OnReadHeader(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    auto self(shared_from_this());  
    if(ec) {  
        LOG("[ClientConnection] OnReadHeader error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
    if (p_->chunked()) {  
        p_->on_chunk_header(headerCb);  
        p_->on_chunk_body(bodyCb);  
        if (!p_->is_done()) {  
            if (timeout_ > 0) {  
                stream_.expires_after(std::chrono::seconds(timeout_));  
            } else {  
                stream_.expires_at(boost::asio::steady_timer::time_point::max());  
            }  
            boost::beast::http::async_read(stream_, buffer_, *p_,  
                boost::beast::bind_front_handler(&ClientConnection::OnChunkBody, self));  
        }  
    }  
}  
  
void ClientConnection::ParserChunkHeader(uint64_t size, boost::beast::string_view extensions,  
    boost::beast::error_code& ec)  
{  
    boost::ignore_unused(extensions);  
    if(ec) {  
        LOG("[ClientConnection] ParserChunkHeader error : %s\n", ec.what().c_str());  
        return;  
    }  
    if(size > (std::numeric_limits<uint64_t>::max)()) {  
        ec = boost::beast::http::error::body_limit;  
        std::cout << "!!!!error::body_limit = " << std::numeric_limits<uint64_t>::max << "; size = " << size << std::endl;  
        return;  
    }  
    chunk.reserve(static_cast<uint64_t>(size));  
    chunk.clear();  
}  
  
size_t ClientConnection::ParserChunkBody(uint64_t remain, boost::beast::string_view body, boost::beast::error_code& ec)  
{  
    if (ec == boost::beast::http::error::body_limit) {  
        std::cout << "ParserChunkBody = " << body << std::endl;  
    }  
    if (ec) {  
        LOG("[ClientConnection] ParserChunkHeader error : %s\n", ec.what().c_str());  
        return body.size();  
    }  
    if(remain == body.size()) {  
        ec = boost::beast::http::error::end_of_chunk;  
    }  
    chunk.append(body.data(), body.size());  
    return body.size();  
}  
  
void ClientConnection::OnChunkBody(boost::beast::error_code ec, size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    auto self(shared_from_this());  
    if(ec) {  
        if (ec != boost::beast::http::error::end_of_chunk) {  
            LOG("[ClientConnection] OnChunkBody error : %s\n", ec.what().c_str());  
  
            if (ec != boost::beast::http::error::end_of_stream)  
                std::cout << "OnChunkBody = " << chunk << std::endl;  
            return DoClose();  
        }  
    }  
    if (isClose) {  
        return;  
    }  
    if (p_->is_done()) {  
        // Gracefully close the socket  
        if (timeout_ != 0 && !p_->keep_alive()) {  
            return DoClose();  
        } else {  
            p_.reset();  
            DoReadHeader();  
        }  
    } else {  
        if (timeout_ > 0) {  
            stream_.expires_after(std::chrono::seconds(timeout_));  
        } else {  
            stream_.expires_at(boost::asio::steady_timer::time_point::max());  
        }  
        boost::beast::http::async_read(stream_, buffer_, *p_,  
            boost::beast::bind_front_handler(&ClientConnection::OnChunkBody, self));  
    }  
}  
```  
   
![image](https://github.com/user-attachments/assets/6d84c8f1-0eb5-43de-b12e-fdb1cef5ed9a)  
  
I added a lot of prints with std::cout, but only `std::cout << "OnChunkBody = " << chunk << std::endl;` is printed.  
  
Dose that mean the error code "body_limit" is not seteed by function "ParserChunkHeader" ? I have no idea.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-18 16:09:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2236962315  

The default body limit is 1MB for requests and 8MB for responses. Are you sure the message doesn't exceed that? You might need to override the default values.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-07-18 16:40:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2237055748  

Note that the body limit is also checked against the sum of received chunks. If their sum exceeds the body limit, the parser will complete with a body_limit error.

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-07-18 16:47:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2237066273  

> The default body limit is 1MB for requests and 8MB for responses. Are you sure the message doesn't exceed that? You might need to override the default values.  
  
How to understand "body limit"? The sum from fist chunk to last chunk? Or one of chunks I reveived?  
Can I set body unlimited? Or if there's a way I can avoid "body limit"?

---

## Comment 4

> Username: xiaoma565  
> Created at: 2024-07-18 16:48:16 UTC  
> Updated at: 2024-07-18 16:55:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2237067438  

> The default body limit is 1MB for requests and 8MB for responses. Are you sure the message doesn't exceed that? You might need to override the default values.  
  
How to understand "body limit"? The sum from fist chunk to last chunk? Or one of chunks I reveived?  
Can I set body unlimited? Or if there's a way I can avoid "body limit"?

---

## Comment 5

> Username: xiaoma565  
> Created at: 2024-07-18 16:54:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2237077561  

> Note that the body limit is also checked against the sum of received chunks. If their sum exceeds the body limit, the parser will complete with a body_limit error.  
  
Can I set body unlimited? Or if there's a way I can avoid "body limit"?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-07-18 16:57:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2237082145  

> Can I set body unlimited? Or if there's a way I can avoid "body limit"?  
  
Yes, use `body_limit( std::size_t(-1) )`

---

## Comment 7

> Username: xiaoma565  
> Created at: 2024-07-19 03:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2238049765  

> > Can I set body unlimited? Or if there's a way I can avoid "body limit"?  
>   
> Yes, use `body_limit( std::size_t(-1) )`  
  
Dose `body_limit( std::size_t(-1) )` mean I am setting "body limit" to "size_t::max"? Or is it unlimited?

---

## Comment 8

> Username: ashtum  
> Created at: 2024-07-19 07:43:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2238569601  

[http::parser::body_limit](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/body_limit.html) takes a `boost::optional< std::uint64_t >`, you can pass `boost::none` to disable body limit.  
```C++  
parser.body_limit(boost::none);  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2024-07-19 15:37:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2239476925  

oh.. LOL. Seems I don't know how my own library works.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2024-07-19 15:38:52 UTC  
> Updated at: 2024-07-19 15:39:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2904#issuecomment-2239479741  

> Dose `body_limit( std::size_t(-1) )` mean I am setting "body limit" to "size_t::max"? Or is it unlimited?  
  
On 64-bit platforms this sets the limit to 16 exabytes, is that sufficient? If your body is stored in a file or a dynamic buffer, then `std::size_t(-1)` is going to be practical limit no matter what. The only case where this is too small is if you are streaming data from an infinite source. For example, a digital radio server that is streaming continuous audio data in the HTTP body.
