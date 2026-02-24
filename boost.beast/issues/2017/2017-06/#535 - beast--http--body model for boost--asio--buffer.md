# #535 - beast::http::body model for boost::asio::buffer [Closed]

> Username: octopus-prime  
> Created at: 2017-06-24 10:48:52 UTC  
> Updated at: 2017-06-24 16:23:28 UTC  
> Closed at: 2017-06-24 16:23:28 UTC  
> Url: https://github.com/boostorg/beast/issues/535  

What is the correct body model to implement this?  
```  
request.body = boost::asio::buffer("Hello, world!");  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-24 12:30:13 UTC  
> Updated at: 2017-06-24 12:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310835847  

http://vinniefalco.github.io/beast/beast/ref/beast__http__string_view_body.html  
  
Or,  
  
http://vinniefalco.github.io/beast/beast/ref/beast__http__buffer_body.html

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-24 12:59:43 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310837146  

```  
void blub1()  
{  
    beast::http::request<beast::http::string_view_body> request;  
    request.body = boost::asio::buffer("Hello, world!");  
}  
void blub2()  
{  
    beast::http::request<beast::http::buffer_body> request;  
    request.body = boost::asio::buffer("Hello, world!");  
}  
```  
  
==>  
  
```  
gcc.compile.c++ bin/gcc-7/release/example/foo.o  
  
    "g++"  -ftemplate-depth-128 -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/workspace/Beast/include" -I"include" -c -o "bin/gcc-7/release/example/foo.o" "example/foo.cpp"  
  
example/foo.cpp: In function ‘void blub1()’:  
example/foo.cpp:168:55: error: no match for ‘operator=’ (operand types are ‘beast::http::string_view_body::value_type {aka boost::basic_string_ref<char, std::char_traits<char> >}’ and ‘boost::asio::const_buffers_1’)  
     request.body = boost::asio::buffer("Hello, world!");  
                                                       ^  
In file included from /home/mike/workspace/Beast/include/beast/core/string.hpp:11:0,  
                 from /home/mike/workspace/Beast/include/beast/core/static_string.hpp:12,  
                 from /home/mike/workspace/Beast/include/beast/core.hpp:29,  
                 from /home/mike/workspace/Beast/include/beast.hpp:13,  
                 from example/foo.cpp:8:  
/usr/include/boost/utility/string_ref.hpp:77:27: note: candidate: constexpr boost::basic_string_ref<charT, traits>& boost::basic_string_ref<charT, traits>::operator=(const boost::basic_string_ref<charT, traits>&) [with charT = char; traits = std::char_traits<char>]  
         basic_string_ref& operator=(const basic_string_ref &rhs) BOOST_NOEXCEPT  
                           ^~~~~~~~  
/usr/include/boost/utility/string_ref.hpp:77:27: note:   no known conversion for argument 1 from ‘boost::asio::const_buffers_1’ to ‘const boost::basic_string_ref<char, std::char_traits<char> >&’  
example/foo.cpp: In function ‘void blub2()’:  
example/foo.cpp:173:55: error: no match for ‘operator=’ (operand types are ‘beast::http::buffer_body::value_type’ and ‘boost::asio::const_buffers_1’)  
     request.body = boost::asio::buffer("Hello, world!");  
                                                       ^  
In file included from /home/mike/workspace/Beast/include/beast/http.hpp:14:0,  
                 from /home/mike/workspace/Beast/include/beast.hpp:14,  
                 from example/foo.cpp:8:  
/home/mike/workspace/Beast/include/beast/http/buffer_body.hpp:30:12: note: candidate: constexpr beast::http::buffer_body::value_type& beast::http::buffer_body::value_type::operator=(const beast::http::buffer_body::value_type&)  
     struct value_type  
            ^~~~~~~~~~  
/home/mike/workspace/Beast/include/beast/http/buffer_body.hpp:30:12: note:   no known conversion for argument 1 from ‘boost::asio::const_buffers_1’ to ‘const beast::http::buffer_body::value_type&’  
/home/mike/workspace/Beast/include/beast/http/buffer_body.hpp:30:12: note: candidate: constexpr beast::http::buffer_body::value_type& beast::http::buffer_body::value_type::operator=(beast::http::buffer_body::value_type&&)  
/home/mike/workspace/Beast/include/beast/http/buffer_body.hpp:30:12: note:   no known conversion for argument 1 from ‘boost::asio::const_buffers_1’ to ‘beast::http::buffer_body::value_type&&’  
...failed gcc.compile.c++ bin/gcc-7/release/example/foo.o...  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-24 13:57:41 UTC  
> Updated at: 2017-06-24 13:59:09 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310840067  

Oh, you **literally** want to assign a `boost::asio::const_buffers_1`. There's no way to do that. I thought you were asking the more general question "which body type represents a non-owning buffer". I don't see much point to a body whose `value_type` is `boost::asio::const_buffers_1`, you can get the same behavior using `buffer_body` and just set fields of the `value_type` to point to your buffer:  
  
```  
void foo(boost::asio::const_buffers_1 cb)  
{  
  response<buffer_body> res;  
  res.body.data = boost::asio::buffer_cast<void const*>(cb);  
  res.body.size = boost::asio::buffer_size(cb);  
  res.body.more = false;  
}  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-24 16:05:03 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847524  

Is this still an issue?

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-06-24 16:07:23 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847658  

Not really -> close :-)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-24 16:07:45 UTC  
> Updated at: 2017-06-24 16:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847674  

I could add these to `buffer_body::value_type`  
```  
        /// Assign a `boost::asio::const_buffers` to `*this`  
        value_type&  
        operator=(boost::asio::const_buffer const& b)  
        {  
            data = boost::asio::buffer_cast<void*>(b);  
            size = boost::asio::buffer_size(b);  
            return *this;  
        }  
  
        /// Assign a `boost::asio::const_buffers_1` to `*this`  
        value_type&  
        operator=(boost::asio::const_buffers_1 const& b)  
        {  
            data = boost::asio::buffer_cast<void*>(b);  
            size = boost::asio::buffer_size(b);  
            return *this;  
        }  
```  
  
I'm working on it now

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-06-24 16:09:06 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847744  

So this would compile?  
```  
void blub2()  
{  
    beast::http::request<beast::http::buffer_body> request;  
    request.body = boost::asio::buffer("Hello, world!");  
}  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-24 16:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847780  

Yes, it should. I'm adding a test for it.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-24 16:10:38 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847826  

Hmm... uh oh, seems like there's a problem. `buffer_body` has to be mutable. I might have to break this up into two classes... ugh

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-24 16:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310847918  

Unfortunately no, this doesn't work. Because `value_type::data` is `void*` not `void const*`. I would have to cast away the const-ness. I am thinking about making two classes  
`buffer_body` which would be `const`, and `mutable_buffer_body` which would be mutable.

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-24 16:18:14 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310848235  

Sounds like the pattern i suggested for strings too:  
  
Strings:  
  
- `mutable_sting_body` for write and read  
- `immutable_sting_body` for write only  
  
Buffers:  
  
- `mutable_buffer_body` for write and read  
- `immutable_buffer_body` for write only

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-24 16:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310848277  

`const` not `immutable`

---

## Comment 13

> Username: octopus-prime  
> Created at: 2017-06-24 16:19:44 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310848333  

But the basic idea is the same - only different names :-)

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-24 16:20:29 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310848369  

I don't like how this turned out, it makes a small issue with the HTTP relay example worse. I am going to have to leave it alone for now, and revisit it later. I will open an issue.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-24 16:23:28 UTC  
> Url: https://github.com/boostorg/beast/issues/535#issuecomment-310848558  

Closing this
