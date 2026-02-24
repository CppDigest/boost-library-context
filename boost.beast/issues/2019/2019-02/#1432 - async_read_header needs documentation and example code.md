# #1432 - async_read_header needs documentation and example code [Open]

> Username: OleksandrKvl  
> Created at: 2019-02-05 18:48:21 UTC  
> Updated at: 2021-02-25 18:41:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1432  

beast version: 181  
compiler: VS2017 15.8.8  
  
Got an error when used async_read_header() like this:  
```c++  
#include <boost/beast.hpp>  
#include <boost/asio.hpp>  
  
void main()  
{  
	namespace http = boost::beast::http;  
	using tcp = boost::asio::ip::tcp;  
  
	boost::asio::io_context ioc;  
	tcp::socket s{ ioc };  
	boost::beast::flat_buffer buffer{ 8192 };  
	http::request<http::dynamic_body> req;  
	http::request_header<> req_header;  
  
	http::async_read_header(  
		s, buffer, req_header, [](const auto & error, auto length){});  
  
	http::async_read_header(  
		s, buffer, req, [](const auto & error, auto length) {});  
}  
```  
Error:  
```  
1>------ Build started: Project: beast_wrapper, Configuration: Debug Win32 ------  
1>beast_wrapper.cpp  
1>beast_wrapper.cpp(258): error C2672: 'boost::beast::http::async_read_header': no matching overloaded function found  
1>beast_wrapper.cpp(259): error C2784: '::boost::asio::async_result<::boost::asio::decay<ReadHandler>::type,void(boost::beast::error_code,size_t)>::return_type boost::beast::http::async_read_header(AsyncReadStream &,DynamicBuffer &,boost::beast::http::basic_parser<isRequest,Derived> &,ReadHandler &&)': could not deduce template argument for 'boost::beast::http::basic_parser<isRequest,Derived> &' from 'boost::beast::http::header<true,Fields>'  
1>        with  
1>        [  
1>            Fields=boost::beast::http::fields  
1>        ]  
1>\boost_1_68_0\boost\beast\http\read.hpp(386): note: see declaration of 'boost::beast::http::async_read_header'  
1>beast_wrapper.cpp(261): error C2672: 'boost::beast::http::async_read_header': no matching overloaded function found  
1>beast_wrapper.cpp(262): error C2784: '::boost::asio::async_result<::boost::asio::decay<ReadHandler>::type,void(boost::beast::error_code,size_t)>::return_type boost::beast::http::async_read_header(AsyncReadStream &,DynamicBuffer &,boost::beast::http::basic_parser<isRequest,Derived> &,ReadHandler &&)': could not deduce template argument for 'boost::beast::http::basic_parser<isRequest,Derived> &' from 'boost::beast::http::message<true,boost::beast::http::dynamic_body,boost::beast::http::fields>'  
1>\boost_1_68_0\boost\beast\http\read.hpp(386): note: see declaration of 'boost::beast::http::async_read_header'  
1>Done building project "beast_wrapper.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```  
What am I doing wrong, should it work with both request and request_header?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-05 18:53:08 UTC  
> Updated at: 2019-02-05 18:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-460758464  

> should it work with both request and request_header?  
  
No, you can only read into a `request`. Try `http::request<http::empty_body> req`.

---

## Comment 2

> Username: OleksandrKvl  
> Created at: 2019-02-05 19:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-460765810  

Thank you for fast response, same error:  
```  
1>\beast_wrapper.cpp(256): error C2672: 'boost::beast::http::async_read_header': no matching overloaded function found  
1>\beast_wrapper.cpp(257): error C2784: '::boost::asio::async_result<::boost::asio::decay<ReadHandler>::type,void(boost::beast::error_code,size_t)>::return_type boost::beast::http::async_read_header(AsyncReadStream &,DynamicBuffer &,boost::beast::http::basic_parser<isRequest,Derived> &,ReadHandler &&)': could not deduce template argument for 'boost::beast::http::basic_parser<isRequest,Derived> &' from 'boost::beast::http::message<true,boost::beast::http::empty_body,boost::beast::http::fields>'  
1>\boost_1_68_0\boost\beast\http\read.hpp(386): note: see declaration of 'boost::beast::http::async_read_header'  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-05 19:26:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-460770292  

Oh, I'm sorry about that.. you can only read into a `parser`. See the documentation page:  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
  
The reason is simple, because if you only read the header there is state information that would be lost if the parser was discarded. Thus Beast requires that you use the parser oriented interface.  
  
There's an example of parsing just the header here:  
```  
    parser<isRequest, buffer_body> p;  
    read_header(stream, buffer, p, ec);  
```  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html

---

## Comment 4

> Username: OleksandrKvl  
> Created at: 2019-02-05 20:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-460784762  

It works, thank you :+1:

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-05 20:10:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-460785333  

I'm going to go ahead and keep this open, after renaming the title, as a reminder when I work on the documentation to provide explicit example code - thanks!

---

## Comment 6

> Username: gsanjeevkumar  
> Created at: 2021-02-24 13:17:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1432#issuecomment-785067696  

Good Work! is the example available, Please!
