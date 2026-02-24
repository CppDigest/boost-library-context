# #655 - can't compile when using async_write_header [Closed]

> Username: Inphi  
> Created at: 2017-07-15 20:24:52 UTC  
> Updated at: 2017-07-16 14:22:45 UTC  
> Closed at: 2017-07-16 14:22:45 UTC  
> Url: https://github.com/boostorg/beast/issues/655  

### Version of Beast  
81  
  
### Steps necessary to reproduce the problem  
Any program with the following will fail to compile:  
```  
http::response<http::empty_body> res;  
res.chunked = true;  
http::response_serializer<http::empty_body> sr{res};  
http::async_write_header(sock, sr, [](const error_code& ec) {});  
```  
  
Error output:  
```  
../include/beast/http/impl/write.ipp:645:5: error: no matching function for call to object of type 'detail::write_op<basic_stream_socket<tcp, stream_socket_service<tcp> >,  
      handler_type<<lambda at raw81.cc:83:48>, void (error_code)>, detail::serializer_is_header_done, false, empty_body, basic_fields<allocator<char> > >'  
    detail::write_op<AsyncWriteStream, handler_type<  
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
raw81.cc:83:13: note: in instantiation of function template specialization 'beast::http::async_write_header<boost::asio::basic_stream_socket<boost::asio::ip::tcp,  
      boost::asio::stream_socket_service<boost::asio::ip::tcp> >, false, beast::http::empty_body, beast::http::basic_fields<std::allocator<char> >, <lambda at  
      raw81.cc:83:48> >' requested here  
      http::async_write_header(self->sock, sr, [self](const error_code& ec) {  });  
            ^  
../include/beast/http/impl/write.ipp:223:5: note: candidate function not viable: requires single argument 'ec', but 2 arguments were provided  
    operator()(error_code ec);  
    ^  
1 error generated.  
```  
  
I naively tried fixing the syntax error at impl/write.ipp:async_write_header by removing the second argument, but beast throws a std::logic_error at buffer_cat.ipp:246 when run.  
The serializer predicate is probably relevant. But i don't really understand the semantics of write_some_op to submit a pr.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-15 21:23:48 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315564245  

Hard to believe...investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-15 21:26:40 UTC  
> Updated at: 2017-07-15 21:26:54 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315564371  

hmmmmmmmmmmmmmmmmmmmmm  
```  
1>------ Build started: Project: http-tests, Configuration: RelWithDebInfo Win32 ------  
1>write.cpp  
1>C:\Users\vinnie\src\Beast\test\http\write.cpp(801): warning C4100: 'ec': unreferenced formal parameter  
1>C:\Users\vinnie\src\Beast\include\beast/http/impl/write.ipp(650): error C2064: term does not  
evaluate to a function taking 2 arguments  
1>C:\Users\vinnie\src\Beast\include\beast/http/impl/write.ipp(650): note: class does not define  
an 'operator()' or a user defined conversion operator to a pointer-to-function or reference-to-function  
that takes appropriate number of arguments  
1>C:\Users\vinnie\src\Beast\test\http\write.cpp(801): note: see reference to function template instantiation 'void beast::http::async_write_header<beast::test::pipe::stream,false,beast::http::empty_body,beast::http::fields,beast::http::write_test::testIssue655::<lambda_1ab909f068f409ca7afe34cdc6304251>>(AsyncWriteStream &,beast::http::serializer<false,beast::http::empty_body,beast::http::fields> &,WriteHandler &&)' being compiled  
1>        with  
1>        [  
1>            AsyncWriteStream=beast::test::pipe::stream,  
1>            WriteHandler=beast::http::write_test::testIssue655::<lambda_1ab909f068f409ca7afe34cdc6304251>  
1>        ]  
1>C:\Users\vinnie\src\Beast\include\beast/core/string.hpp(107): note: see reference to class template instantiation 'boost::basic_string_view<char,std::char_traits<char>>' being compiled  
1>Done building project "http-tests.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-15 21:50:27 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315565609  

Please try the branch **issue655**, thanks  
https://github.com/vinniefalco/Beast/commit/8dd1eec45fe999ac4dcc351390cc6e8d664d88bd

---

## Comment 4

> Username: Inphi  
> Created at: 2017-07-16 02:58:55 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315581505  

it compiles now, but it fails at runtime.  
stacktrace:  
```  
#0  0x00007ffff68f2e55 in raise () from /lib64/libc.so.6  
#1  0x00007ffff68f4a5a in abort () from /lib64/libc.so.6  
#2  0x00007ffff68eb6f7 in __assert_fail_base () from /lib64/libc.so.6  
#3  0x00007ffff68eb7a2 in __assert_fail () from /lib64/libc.so.6  
#4  0x0000000000447c20 in boost::detail::variant::forced_return<beast::consuming_buffers<beast::detail::buffers_ref<beast::buffer_cat_view<boost::asio::const_buffers_1, boost::asio::const_buffers_1, boost::asio::const_buffers_1, beast::http::basic_fields<std::allocator<char> >::reader::field_range, beast::http::chunk_crlf> > >*> () at /usr/include/boost/variant/detail/forced_return.hpp:39  
  
... snip  
#13 0x000000000048e185 in boost::get<beast::consuming_buffers<beast::detail::buffers_ref  
  ....  
 beast::http::chunk_crlf> > > (operand=...)  
   at /usr/include/boost/variant/get.hpp:284  
#14 0x000000000048d9f6 in beast::http::serializer<false, beast::http::empty_body, beast::http::basic_fields<std::allocator<char> > >::consume (this=0x7ffff5eb3888, n=47)  
    at ../include/beast/http/impl/serializer.ipp:356  
```  
  
You can close this if you'd rather have a different Issue to deal with the runtime problem.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-16 03:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315582842  

I'm going to need more information. Which program are you running? Is this one of the tests? Whats the latest version that doesn't crash?

---

## Comment 6

> Username: Inphi  
> Created at: 2017-07-16 04:48:48 UTC  
> Updated at: 2017-07-16 04:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315585368  

It's the the program in the original post that crashed.  
Version 75 is the latest that works for me, which is coincidentally the latest release (on master) that compiles w/ async_write_header

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-16 05:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315586479  

How does this compile for you?  
>res.chunked = true;  
  
`chunked` is a function....

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-16 05:21:05 UTC  
> Url: https://github.com/boostorg/beast/issues/655#issuecomment-315586640  

I can't get it to crash using your code. Please try this branch, run the http-tests:  
https://github.com/vinniefalco/Beast/commits/v81  
  
Your code is here https://github.com/vinniefalco/Beast/commit/b90a87e6145e8da55dbdffce9105e0d816abefc6#diff-cb73cbb88979ae8d7fe168476511f141R801
