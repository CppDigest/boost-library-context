# #1264 - bad_version error with boost::beast::http::async_read_some [Closed]

> Username: Maratk1n  
> Created at: 2018-10-08 16:11:50 UTC  
> Updated at: 2018-11-28 02:09:49 UTC  
> Closed at: 2018-11-28 02:09:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1264  

Hello!  
  
I am trying to read data asynchronously and write it to a file.  
The sequence is as follows:  
1. Read the header  
2. Read the target using the function `boost::beast::http::async_read_some`.  
But I get an error `boost::asio::error::bad_version` when trying to read the target. I have not found a single example of using the function `async_read_some`. How to use it correctly?  
  
My implementation:  
  
```  
void AsyncHttpClient::onReadHeader(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
  boost::ignore_unused(bytes_transferred);  
  
  if(ec) {  
    return fail(ec, "read header");  
  }  
  std::cout << res_.get().base() << std::endl;  
  
  if (file_.is_open()) {  
    file_ << res_.get().body();  
  }  
  
  // If we read the header, we can start reading content chunk by chunk.  
  if (https_mode_) {  
    // Receive the HTTP response  
    boost::beast::http::async_read_some(stream_, buffer_, chunk_res_, std::bind(&AsyncHttpClient::onRead, this, std::placeholders::_1, std::placeholders::_2));  
  }  
  else {  
    // Receive the HTTP response  
    boost::beast::http::async_read_some(socket_, buffer_, chunk_res_, std::bind(&AsyncHttpClient::onRead, this, std::placeholders::_1, std::placeholders::_2));  
  }  
}  
void AsyncHttpClient::onRead(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
  boost::ignore_unused(bytes_transferred);  
  
  if(ec && ec.value() != boost::asio::error::eof) {  
    return fail(ec, "read content"); // here I get error "bad version"  
  }  
  
  if (ec.value() != boost::asio::error::eof) { // continue loading  
    if (file_.is_open()) {  
      file_ << chunk_res_.get().body();  
    }  
  
    if (https_mode_) {  
      // Receive the HTTPS response  
      boost::beast::http::async_read_some(stream_, buffer_, chunk_res_, std::bind(&AsyncHttpClient::onRead, this, std::placeholders::_1, std::placeholders::_2));  
    }  
    else {  
      // Receive the HTTP response  
      boost::beast::http::async_read_some(socket_, buffer_, chunk_res_, std::bind(&AsyncHttpClient::onRead, this, std::placeholders::_1, std::placeholders::_2));  
    }  
  }  
  else {  
    file_.close();  
    std::cout << std::endl;  
    std::cout << "Download completed." << std::endl;  
    if (https_mode_) {  
      // Gracefully close the stream  
      stream_.async_shutdown(std::bind(&AsyncHttpClient::onShutdown, this, std::placeholders::_1));  
    }  
    else {  
      // Gracefully close the socket  
      socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both, ec);  
      // not_connected happens sometimes so don't bother reporting it.  
      if(ec && ec != boost::system::errc::not_connected) {  
          return fail(ec, "shutdown");  
      }  
      // If we get here then the connection is closed gracefully  
    }  
  }  
}  
```  
Variables types:  
```  
boost::beast::flat_buffer buffer_;  
boost::beast::http::response_parser<boost::beast::http::string_body> res_;  
boost::beast::http::response_parser<boost::beast::http::string_body> chunk_res_;  
```  
Thanks for any help!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-08 16:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-427905854  

What do the incoming bytes look like?

---

## Comment 2

> Username: Maratk1n  
> Created at: 2018-10-08 17:05:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-427909505  

> What do the incoming bytes look like?  
  
In `AsyncHttpClient::onReadHeader` I get:  
```  
HTTP/1.0 200 OK  
Server: SimpleHTTP/0.6 Python/3.6.6  
Date: Mon, 08 Oct 2018 17:04:04 GMT  
Content-type: image/jpeg  
Content-Length: 60241  
Last-Modified: Mon, 08 Oct 2018 11:39:51 GMT  
```  
but in `AsyncHttpClient::onRead` `bytes_transferred=0`

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-08 17:09:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-427910580  

Are you trying to use the `response_parser` more than once?  
  
Also, you might want to use `http::file_body` if you are writing to a file.

---

## Comment 4

> Username: Maratk1n  
> Created at: 2018-10-08 17:16:25 UTC  
> Updated at: 2018-10-08 18:05:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-427912847  

> Are you trying to use the `response_parser` more than once?  
  
I'm using `boost::beast::http::response_parser<boost::beast::http::string_body> res_;` for parse header, and using `boost::beast::http::response_parser<boost::beast::http::string_body> chunk_res_;` for parse parts of content with `async_read_some`.   
  
> Also, you might want to use `http::file_body` if you are writing to a file.  
  
I tried to do it:  
`boost::beast::http::response_parser<boost::beast::http::file_body> chunk_res_;`, but get error in this place:  
`file_ << chunk_res_.get().body();`  
Message of error:  
`/usr/include/c++/7/ostream:682: error: no type named ‘type’ in ‘struct std::enable_if<false, std::basic_ostream<char>&>’`

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-10-08 18:33:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-427936504  

You should not be using two different parsers. Just use `res_` for both the header and the body. And if you want to re-use the parser you have to destroy it and reconstruct it. You can do that easily by putting it inside an `optional` and calling `emplace` every time you need to reuse it.

---

## Comment 6

> Username: Maratk1n  
> Created at: 2018-10-09 10:05:37 UTC  
> Updated at: 2018-10-09 11:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428135568  

> You should not be using two different parsers. Just use `res_` for both the header and the body. And if you want to re-use the parser you have to destroy it and reconstruct it. You can do that easily by putting it inside an `optional` and calling `emplace` every time you need to reuse it.  
  
Thank for your answer! Can you give me short example, please?   
I tried do as well as [there](https://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/server/fast/http_server_fast.cpp), but it can't help me.  
Now I just replace `chunk_res_` to `res_` and catch error:  
  
`typename boost::asio::async_result<typename std::decay<ReadHandler>::type, void(boost::system::error_code, long unsigned int)>::return_type boost::beast::http::async_read_some(AsyncReadStream&, DynamicBuffer&, boost::beast::http::basic_parser<isRequest, Derived>&, ReadHandler&&) [with AsyncReadStream = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; bool isRequest = false; Derived = boost::beast::http::parser<false, boost::beast::http::basic_string_body<char>, std::allocator<char> >; ReadHandler = std::_Bind<void (AsyncHttpClient::*(AsyncHttpClient*, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, long unsigned int)>; typename boost::asio::async_result<typename std::decay<ReadHandler>::type, void(boost::system::error_code, long unsigned int)>::return_type = void]: Assertion `! parser.is_done()' failed.`  
  
And how I can set max buffer size when work with `async_read_some`? Now used 512 bytes.  
  
upd:  
I found the cause of the error. In `AsyncHttpClient::onRead` change  
```  
if (ec.value() != boost::asio::error::eof) { // continue loading  
  //...  
}  
else {  
  //..  
}  
```  
to   
```  
if (!res_.is_done()) { // continue loading  
  //...  
}  
else {  
  //..  
}  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-10-09 17:00:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428270677  

Use  
```  
boost::optional<boost::beast::http::response_parser<boost::beast::http::string_body>> res_;  
```  
  
And then, to reset the parser for reuse, do:  
```  
res_.emplace();  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-10-09 17:18:19 UTC  
> Updated at: 2018-10-09 17:18:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428276537  

Also, this is wrong:  
```  
if (ec.value() != boost::asio::error::eof) { // continue loading  
```  
Because you are doing a simple integer comparison instead of going through the proper `operator==` overload which also checks the error category. Instead, you should write:  
```  
if (ec != boost::asio::error::eof) { // continue loading  
```  
Not only is this correct, but it is also simpler. A double win!

---

## Comment 9

> Username: Maratk1n  
> Created at: 2018-10-10 10:02:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428513735  

> Also, this is wrong:  
>   
> ```  
> if (ec.value() != boost::asio::error::eof) { // continue loading  
> ```  
> Because you are doing a simple integer comparison instead of going through the proper `operator==` overload which also checks the error category. Instead, you should write:  
>   
> ```  
> if (ec != boost::asio::error::eof) { // continue loading  
> ```  
> Not only is this correct, but it is also simpler. A double win!  
  
Excellent answers! But this trick can't help resolve previous problem. I get same error.  
  
And what about buffer size as in Boost?  
[There](https://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/buffered_read_stream/async_read_some.html) we can configure buffer and its size. Is there such a possibility in a Beast?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-10-10 13:27:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428570888  

> Assertion! parser.is_done()' failed  
  
This is fixed by putting the parser in an `optional` and constructing it again via `emplace` after you are done with each message.  
  
> And what about buffer size as in Boost? There we can configure buffer and its size. Is there such a possibility in a Beast?  
  
What is the question?

---

## Comment 11

> Username: Maratk1n  
> Created at: 2018-10-10 13:41:42 UTC  
> Updated at: 2018-10-10 13:42:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428576187  

> What is the question?  
  
In Boost we can do   
`socket.async_read_some(boost::asio::buffer(data, size), handler);`  
So, can we do something like this?  
Now Beast read content after every 512 bytes part by part. Can I increase this limit?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-10-10 13:46:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-428577949  

See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_http/buffer_oriented_parsing.html

---

## Comment 13

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1264#issuecomment-441859449  

This issue has been open for a while with no activity, has it been resolved?
