# #1106 - How to read post request body as std::string [Closed]

> Username: snahmad  
> Created at: 2018-04-26 14:56:29 UTC  
> Updated at: 2018-07-02 13:13:23 UTC  
> Closed at: 2018-04-26 21:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1106  

### Version of Beast  
167  
  
### Steps necessary to reproduce the problem  
  
void process_request(http::request<request_body_t, http::basic_fields<alloc_t>> const& req)  
		{  
			std::string str = req.  
			switch (req.method())  
			{  
			case http::verb::post:  
                                  
                              std::string post_body = req.?  
				break;  
			default:  
				break;  
			}  
		}

---

## Comment 1

> Username: knejadfard  
> Created at: 2018-04-26 16:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384695735  

What is the body type of this request?  
  
If the request is of type `http::request<http::string_body>`, then calling the `body()` member function of the request will return a `std::basic_string`:  
```std::string body = req.body();```  
  
A more real-world scenario would be to read the header of the request first, determine its content type, and then read the body accordingly. I found [this example](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/more_examples/change_body_type.html) to be quite useful for scenarios like this.

---

## Comment 2

> Username: snahmad  
> Created at: 2018-04-26 16:23:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384703297  

I am looking at this eample of adding handling of http post request and extract body as std::string  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/server/fast/http_server_fast.cpp  
  
Can you add code to this example please?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-26 16:26:59 UTC  
> Updated at: 2018-04-26 16:27:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384704594  

The type of  `string_response_->body()` will be `std::string`.

---

## Comment 4

> Username: snahmad  
> Created at: 2018-04-26 16:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384709571  

I am talking about request body not response body.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-26 16:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384711346  

```  
using request_body_t = std::string;  
```

---

## Comment 6

> Username: snahmad  
> Created at: 2018-04-26 16:59:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384714633  

Changing the example  
//using request_body_t = http::basic_dynamic_body<boost::beast::flat_static_buffer<1024 * 1024>>;  
using request_body_t = std::string;  
  
break compilation.  
  
Do you have any example C++ class Http Server with thread to handle both http get file and post

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-26 17:16:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384719782  

No, and if you don't post the output of the compiler which shows the error, I can't help you.

---

## Comment 8

> Username: snahmad  
> Created at: 2018-04-26 19:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384764113  

c:\boost_1_67_0\boost\beast\http\parser.hpp(57): error C2338: BodyReader requirements not met  
1>c:\boost_1_67_0\boost\type_traits\is_nothrow_move_constructible.hpp(27): note: see reference to class template instantiation 'boost::beast::http::parser<true,http_worker::request_body_t,http_worker::alloc_t>' being compiled  
1>c:\boost_1_67_0\boost\optional\optional.hpp(187): note: see reference to class template instantiation 'boost::is_nothrow_move_constructible<T>' being compiled  
1>        with  
1>        [  
1>            T=boost::beast::http::parser<true,http_worker::request_body_t,http_worker::alloc_t>  
1>        ]  
1>c:\boost_1_67_0\boost\optional\optional.hpp(831): note: see reference to class template instantiation 'boost::optional_detail::optional_base<T>' being compiled  
1>        with  
1>        [  
1>            T=boost::beast::http::parser<true,http_worker::request_body_t,http_worker::alloc_t>  
1>        ]  
1>c:\users\naeem\documents\visual studio 2017\projects\boost_beast\boost_beast\boost_beast.cpp(110): note: see reference to class template instantiation 'boost::optional<boost::beast::http::parser<true,http_worker::request_body_t,http_worker::alloc_t>>' being compiled  
1>c:\boost_1_67_0\boost\asio\use_future.hpp(138): note: see reference to class template instantiation 'boost::asio::use_future_t<std::allocator<void>>::std_allocator_void' being compiled  
1>c:\boost_1_67_0\boost\asio\use_future.hpp(146): note: see reference to class template instantiation 'boost::asio::use_future_t<std::allocator<void>>' being compiled  
1>c:\boost_1_67_0\boost\beast\core\string.hpp(87): note: see reference to class template instantiation 'boost::basic_string_view<char,std::char_traits<char>>' being compiled  
1>c:\boost_1_67_0\boost\beast\http\parser.hpp(66): error C2039: 'reader': is not a member of 'std::basic_string<char,std::char_traits<char>,std::allocator<char>>'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.11.25503\include\xstring(4079): note: see declaration of 'std::basic_string<char,std::char_traits<char>,std::allocator<char>>'  
1>c:\boost_1_67_0\boost\beast\http\parser.hpp(66): error C3646: 'rd_': unknown override specifier

---

## Comment 9

> Username: snahmad  
> Created at: 2018-04-26 20:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384778871  

Any idea about these compiler errors.

---

## Comment 10

> Username: snahmad  
> Created at: 2018-04-26 20:30:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384779128  

I cannot find any working example Http server which handle http post. get post body as std::string and send back reply.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-04-26 20:31:53 UTC  
> Updated at: 2018-04-26 20:47:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384779413  

I am so sorry...I gave you incorrect code. This is the correct version:  
```  
using request_body_t = boost::beast::http::string_body;  
```  
I tested this in MSVC and it compiled.

---

## Comment 12

> Username: snahmad  
> Created at: 2018-04-26 20:52:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384785132  

ok. working thanks.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-04-26 21:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-384799822  

Glad it works! I'll go ahead and close this since it is resolved, feel free to open new issues as needed - Thanks.

---

## Comment 14

> Username: Shoaib-Raza  
> Created at: 2018-07-02 10:20:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-401752710  

Hi,  
  
I am using Boost beast 1.67 version, example Http Server 3.  
My problem is that, body variable is missing in Request struct in request.hpp.  
How can I get the body as string in Http Server 3 example?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-07-02 13:13:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1106#issuecomment-401799761  

That's because you're looking at the Asio examples not the Beast examples.
