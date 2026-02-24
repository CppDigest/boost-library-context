# #462 - Stock http_ssl_example throw 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >' [Closed]

> Username: pierreN  
> Created at: 2017-06-10 09:51:38 UTC  
> Updated at: 2017-06-11 02:46:10 UTC  
> Closed at: 2017-06-11 02:46:10 UTC  
> Url: https://github.com/boostorg/beast/issues/462  

Hi,  
  
  
I've seen the talk of Beast on CppCon and trying it out for the first time.  
  
The stock http_ssl_example which connects to github doesn't work on my system (Ubuntu artful, libboost 1.63):  
  
  
```  
  
pierre : 76402f7d , master $ ./examples/ssl/http-ssl-example  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >'  
  what():  bad chunk  
[1]    5411 abort (core dumped)  ./examples/ssl/http-ssl-example  
  
 pierre : 76402f7d , master $ gdb ./examples/ssl/http-ssl-example       
[...]  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ./examples/ssl/http-ssl-example...(no debugging symbols found)...done.  
(gdb) run  
Starting program: /home/pierre/btc/Beast/build/examples/ssl/http-ssl-example  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >'  
  what():  bad chunk  
  
Program received signal SIGABRT, Aborted.  
__GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:58  
58      ../sysdeps/unix/sysv/linux/raise.c: No such file or directory.  
(gdb) where  
#0  __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:58  
#1  0x00007ffff6bdb37a in __GI_abort () at abort.c:89  
#2  0x00007ffff7432065 in __gnu_cxx::__verbose_terminate_handler() () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#3  0x00007ffff742fc56 in ?? () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#4  0x00007ffff742fca1 in std::terminate() () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#5  0x00007ffff742fee4 in __cxa_throw () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#6  0x0000555555578ee2 in void boost::throw_exception<boost::exception_detail::error_info_injector<boost::system::system_error> >(boost::exception_detail::error_info_injector<boost::system::system_error> const&) ()  
#7  0x00005555555733ef in void boost::exception_detail::throw_exception_<boost::system::system_error>(boost::system::system_error const&, char const*, char const*, int) ()  
#8  0x000055555556f7f2 in void beast::http::read<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >&>, beast::basic_multi_buffer<std::allocator<char> >, false, beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::allocator<char> > >, beast::http::basic_fields<std::allocator<char> > >(boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >&>&, beast::basic_multi_buffer<std::allocator<char> >&, beast::http::message<false, beast::http::basic_dynamic_body<beast::basic_multi_buffer<std::allocator<char> > >, beast::http::basic_fields<std::allocator<char> > >&) ()  
#9  0x000055555555d1d1 in main ()  
```  
  
Any idea of what's happening?  
Thanks,

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-10 12:32:28 UTC  
> Updated at: 2017-06-10 12:39:04 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307562447  

Looks like you are using version 48 of Beast or earlier which had a bug. This went into version 49: https://github.com/vinniefalco/Beast/commit/4df68852a7936460f27a4377b97773af3bb1c049

---

## Comment 2

> Username: pierreN  
> Created at: 2017-06-10 12:55:14 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307563481  

Thanks for the quick answer!  
I've just checked out the most recent version of the repository when doing this (76402f7d as it is written in the code above).  
  
And:  
```  
$ git log | grep "Fix chunk header parsing"  
    Fix chunk header parsing  
$  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-10 12:58:50 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307563646  

I can reproduce the issue! Now I am wondering, why isn't the SSL example running as part of the tests!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-10 13:10:43 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307564212  

There are several problems here. The biggest one, is that the SSL example is not being run as part of the tests. Probably the Travis script is misconfigured. The other problem is that the parser code for flattening a buffer sequence whose length is greater than one has a bug, nice find! This workaround will make the example run: https://github.com/vinniefalco/Beast/commit/0282007908a4ee0912ea69865c0d2ce5247fc5c2  
  
I am working on a real fix

---

## Comment 5

> Username: pierreN  
> Created at: 2017-06-10 13:13:21 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307564323  

Thanks!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-11 02:46:10 UTC  
> Url: https://github.com/boostorg/beast/issues/462#issuecomment-307602503  

Fixed in version **53**
