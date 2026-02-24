# #902 - Compiler warnings with beast from boost 1_66_0_b1 and gcc 7.2.0 [Closed]

> Username: octopus-prime  
> Created at: 2017-11-23 11:04:15 UTC  
> Updated at: 2017-11-27 11:07:44 UTC  
> Closed at: 2017-11-27 09:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/902  

Tried to use beast (v144) from boost 1_66_0_b1 and got these compiler warnings:  
  
```  
In file included from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/buffers_cat.hpp:117:0,  
                 from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core.hpp:18,  
                 from source/client.cpp:12:  
/home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/impl/buffers_cat.ipp: In function ‘std::size_t boost::asio::buffer_size(const BufferSequence&) [with BufferSequence = boost::beast::detail::buffers_ref<boost::beast::buffers_cat_view<boost::asio::const_buffer, boost::asio::const_buffer, boost::asio::const_buffer, boost::beast::http::basic_fields<std::allocator<char> >::writer::field_range, boost::beast::http::chunk_crlf> >]’:  
/home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/impl/buffers_cat.ipp:255:29: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             return *iter<I>();  
                             ^  
In file included from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/detail/type_traits.hpp:14:0,  
                 from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/type_traits.hpp:15,  
                 from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core/bind_handler.hpp:14,  
                 from /home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/beast/core.hpp:15,  
                 from source/client.cpp:12:  
/home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/asio/buffer.hpp:550:12: note: ‘*((void*)& iter +16)’ was declared here  
   Iterator iter = begin;  
            ^~~~  
```  
  
The called compile command:  
  
`  
"g++" -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0" -I"include" -c -o "bin/gcc-7/release/source/client.o" "source/client.cpp"  
`  
  
Compiler:  
  
```  
gcc (Ubuntu 7.2.0-8ubuntu3) 7.2.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
Compiled code:  
  
```  
class client_impl : public virtual client_base  
{  
public:  
    client_impl(const string_t& host, const string_t& port)  
    :  
        _service(),  
        _socket(_service),  
		_fields(),  
		_buffer()  
    {  
        boost::asio::ip::tcp::resolver resolver{_service};  
        boost::asio::connect(_socket, resolver.resolve({host, port}));  
  
        _fields.set(http::field::host, host);  
        _fields.set(http::field::user_agent, "libhessian/1.0 (" BOOST_BEAST_VERSION_STRING ")");  
        _fields.set(http::field::content_type, "x-application/hessian");  
    }  
  
    virtual value_t call(const string_t& service, const string_t& method, const list_t& arguments) override  
    {  
    	http::request<http::string_body> request(http::verb::post, service, 11, generate(method, arguments), _fields);  
        request.prepare_payload();  
        http::write(_socket, request);  
  
        http::response<http::string_body> response;  
        http::read(_socket, _buffer, response);  
  
        const content_t content = parse(response.body());  
        return std::visit(content_visitor(), content);  
    }  
  
private:  
    boost::asio::io_service _service;  
    boost::asio::ip::tcp::socket _socket;  
    http::fields _fields;  
    boost::beast::flat_buffer _buffer;  
};  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-23 14:38:56 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-346634760  

Yep, that's a false positive. The code is correct. I'm not sure what to do about it, do you have any ideas?

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-11-23 15:18:21 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-346644271  

At `buffers_cat.ipp:255` is `*iter<0>()` valid?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-23 16:43:34 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-346663146  

`*iter<0>` is valid when `n_ == 0`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-11-23 17:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-346674496  

This did not seem to help  
https://github.com/vinniefalco/beast/commit/439d0a8c8e8274569af7d79de27ca5084b17ab1a

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-11-26 21:58:19 UTC  
> Updated at: 2017-11-26 21:58:27 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-347041789  

Please try version **146**, I believe the warnings have been addressed: https://github.com/boostorg/beast/commit/5d83c79be408c8a29dd7eec413e9dc699648cdba

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-11-27 09:53:40 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-347131593  

Excellent! With version 146 the warnings disappear.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-11-27 09:55:57 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-347132203  

Will version 146+ be available in boost 1.66?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-11-27 11:07:44 UTC  
> Url: https://github.com/boostorg/beast/issues/902#issuecomment-347150208  

>Will version 146+ be available in boost 1.66?  
  
Unfortunately not. Version 146 has not had enough testing, I think it is too risky to patch it into the boost 1.66 beta at the last minute.
