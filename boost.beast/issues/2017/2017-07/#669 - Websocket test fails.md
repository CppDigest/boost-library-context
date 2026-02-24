# #669 - Websocket test fails [Closed]

> Username: xsacha  
> Created at: 2017-07-22 04:40:41 UTC  
> Updated at: 2017-07-28 04:06:59 UTC  
> Closed at: 2017-07-28 04:06:59 UTC  
> Url: https://github.com/boostorg/beast/issues/669  

### Version of Beast  
tag b84  
  
### Steps necessary to reproduce the problem  
  
Compile (with CMake and Boost 1.64)  
  
### All relevant compiler information  
  
Apple LLVM version 8.1.0 (clang-802.0.41)  
  
  
Full error:  
```  
Scanning dependencies of target websocket-tests  
[ 45%] Building CXX object test/websocket/CMakeFiles/websocket-tests.dir/__/__/extras/beast/unit_test/main.cpp.o  
[ 46%] Building CXX object test/websocket/CMakeFiles/websocket-tests.dir/doc_snippets.cpp.o  
In file included from /Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:18:  
In file included from /Users/sacha/source/Beast/include/beast/websocket.hpp:16:  
In file included from /Users/sacha/source/Beast/include/beast/websocket/stream.hpp:3731:  
/Users/sacha/source/Beast/include/beast/websocket/impl/read.ipp:691:11: error:   
      no matching constructor for initialization of  
      'beast::detail::bound_handler<(lambda at  
      /Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:210:9), const  
      std::__1::placeholders::__ph<1> &>'  
        : h_(std::forward<DeducedHandler>(h))  
          ^  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/sacha/source/Beast/include/beast/websocket/impl/read.ipp:849:5: note: in  
      instantiation of function template specialization  
      'beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp,  
      boost::asio::stream_socket_service<boost::asio::ip::tcp> >  
      >::read_op<beast::basic_multi_buffer<std::__1::allocator<char> >,  
      beast::detail::bound_handler<(lambda at  
      /Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:210:9), const  
      std::__1::placeholders::__ph<1> &>  
      >::read_op<beast::detail::bound_handler<(lambda at  
      /Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:210:9),  
      std::__1::placeholders::__ph<1> &> >' requested here  
    read_op<  
    ^  
/Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:209:8: note: in  
      instantiation of function template specialization  
      'beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp,  
      boost::asio::stream_socket_service<boost::asio::ip::tcp> >  
      >::async_read<beast::basic_multi_buffer<std::__1::allocator<char> >,  
      (lambda at  
      /Users/sacha/source/Beast/test/websocket/doc_snippets.cpp:210:9)>'  
      requested here  
    ws.async_read(buffer,  
       ^  
/Users/sacha/source/Beast/include/beast/core/detail/bind_handler.hpp:100:5: note:   
      candidate constructor not viable: no known conversion from  
      'bound_handler<[...], std::__1::placeholders::__ph<1> &>' to  
      'bound_handler<[...], const std::__1::placeholders::__ph<1> &>' for 1st  
      argument  
    bound_handler(bound_handler&&) = default;  
    ^  
/Users/sacha/source/Beast/include/beast/core/detail/bind_handler.hpp:101:5: note:   
      candidate constructor not viable: no known conversion from  
      'bound_handler<[...], std::__1::placeholders::__ph<1> &>' to 'const  
      bound_handler<[...], const std::__1::placeholders::__ph<1> &>' for 1st  
      argument  
    bound_handler(bound_handler const&) = default;  
    ^  
/Users/sacha/source/Beast/include/beast/core/detail/bind_handler.hpp:105:5: note:   
      candidate constructor template not viable: requires 2 arguments, but 1 was  
      provided  
    bound_handler(  
    ^  
1 error generated.  
make[2]: *** [test/websocket/CMakeFiles/websocket-tests.dir/doc_snippets.cpp.o] Error 1  
make[1]: *** [test/websocket/CMakeFiles/websocket-tests.dir/all] Error 2  
make: *** [all] Error 2  
  
```

---

## Comment 1

> Username: xsacha  
> Created at: 2017-07-22 05:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/669#issuecomment-317155954  

Issue looks to be on include/beast/websocket/impl/read.ipp, line 853  
I dropped the const and it all worked perfectly.  
That is, I changed:  
'bound_handler<[...], std::__1::placeholders::__ph<1> const&>'  
to  
'bound_handler<[...], std::__1::placeholders::__ph<1> &>'

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-28 04:06:59 UTC  
> Url: https://github.com/boostorg/beast/issues/669#issuecomment-318553523  

This is fixed in **version 86**
