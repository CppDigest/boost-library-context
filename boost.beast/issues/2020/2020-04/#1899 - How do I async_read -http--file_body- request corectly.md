# #1899 - How do I async_read <http::file_body> request corectly? [Closed]

> Username: HotBabyccino  
> Created at: 2020-04-10 06:48:31 UTC  
> Updated at: 2020-05-01 15:57:35 UTC  
> Closed at: 2020-05-01 15:57:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1899  

I'm new to Boost and trying to add a POST request to one of the client example. But I failed to read a `http::quest<http::file_body>` request on the server side.  
  
`Assertion failed: body_.file_.is_open(), file D:\Boost C++ Projects\AsioServre\include\boost _1_72_0\boost\beast\http\impl\file_body_win32.hpp, line 184`  
  
I read the Boost code in this _file_body_win32.hpp_ file, it just simply tells me the file is open. How come? And the werid thing is if I change `content_type: "text/plain"` to `content_type: "image/jpeg"` (body opens an image), then the assertion will be passed, but with body limit exceeded error.  
  
**Client Side:**  
```  
std::string path = "C:\\Users\\Jinx\\Desktop\\Damn\\test.txt";  
body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
req_to.version(version);  
req_to.method(http::verb::post);  
req_to.target(target);  
req_to.set(http::field::host, host);  
req_to.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
req_to.set(http::field::content_type, "text/plain");  
req_to.content_length(body.size());  
req_to.keep_alive(req_.keep_alive());  
req_to.body() = std::move(body);  
req_to.prepare_payload();  
  
http::async_write(stream_, req_to, beast::bind_front_handler(&session::on_write, shared_from_this()));  
```  
  
On the server side, I have no idea what type of buffer I should use. I've tried a multi buffer, it didn't work either.   
  
**Server Side:**  
```  
beast::tcp_stream stream_;  
beast::flat_buffer buffer_;   
std::shared_ptr<std::string const> doc_root_;  
http::request<http::file_body> req_from;  
  
http::async_read(stream_, buffer_, req_from, beast::bind_front_handler(&session::on_read, shared_from_this()));  
```  
  
According to the [http::file_body](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html) document:  
  
> When serializing, the implementation will read the file and present those octets as the body content.  
  
How can the file opened by the server?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-10 10:47:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1899#issuecomment-611981073  

Hi @HotBabyccino,  
  
I'd love to help but I need more information.   
  
What might be useful to me is if you can write a best-effort single-file server and client (they could even be in the same file if you know how to do that), make it available in github and then link it here.  
  
I'll then fork it and see if I can get it working for you.  
  
R
