# #159 - 98% cpu stuck in parse_op() [Closed]

> Username: bitbugprime  
> Created at: 2016-10-28 16:18:55 UTC  
> Updated at: 2016-11-08 12:15:07 UTC  
> Closed at: 2016-11-08 12:04:01 UTC  
> Url: https://github.com/boostorg/beast/issues/159  

Not sure how to debug this, but I'm seeing my app pegging the CPU (and not responding to anything) even though it's doing nothing. It looks stuck in beast::http::detail::parse_op.  
  
Sorry for the formatting, there is a backtrace down there but github isn't maintaining formatting.  
  
(gdb) info threads  
  Id   Target Id         Frame   
  18   Thread 0x7f34c7dbb700 (LWP 30420) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  17   Thread 0x7f34c55b6700 (LWP 31866) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  16   Thread 0x7f34ae7fc700 (LWP 31876) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  15   Thread 0x7f34ad7fa700 (LWP 31880) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  14   Thread 0x7f34a67fc700 (LWP 31884) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  13   Thread 0x7f3483fff700 (LWP 31888) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  12   Thread 0x7f34a4ff9700 (LWP 31917) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  11   Thread 0x7f3479ffb700 (LWP 31920) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  10   Thread 0x7f34797fa700 (LWP 31923) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  9    Thread 0x7f34c75ba700 (LWP 31925) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  8    Thread 0x7f34c6db9700 (LWP 31926) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  7    Thread 0x7f34787f8700 (LWP 31927) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  6    Thread 0x7f34c65b8700 (LWP 31928) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  5    Thread 0x7f34affff700 (LWP 31929) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  4    Thread 0x7f34af7fe700 (LWP 31930) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  3    Thread 0x7f347bfff700 (LWP 31932) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
  2    Thread 0x7f34c4db5700 (LWP 31933) "dbhealth" pthread_cond_wait@@GLIBC_2.3.2 ()  
    at ../nptl/sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185  
- 1    Thread 0x7f34c90f2780 (LWP 30416) "dbhealth" 0x000000000056045c in beast::http::detail::parse_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, beast::http::parser_v1<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >, beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, false, beast::http::string_body, beast::http::basic_headersstd::allocator<char >, tlo::asio::detail::promise_handler<void> > >::operator()(boost::system::error_code, unsigned long, bool) ()  
  (gdb) bt  
  #0  0x000000000056045c in beast::http::detail::parse_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, beast::http::parser_v1<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >, beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, false, beast::http::string_body, beast::http::basic_headersstd::allocator<char >, tlo::asio::detail::promise_handler<void> > >::operator()(boost::system::error_code, unsigned long, bool) ()  
  #1  0x0000000000561671 in boost::asio::detail::reactive_socket_recv_opbeast::basic_streambuf<std::allocator<char >::mutable_buffers_type, beast::http::detail::parse_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, beast::http::parser_v1<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >, beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, beast::basic_streambufstd::allocator<char >, false, beast::http::string_body, beast::http::basic_headersstd::allocator<char >, tlo::asio::detail::promise_handler<void> > > >::do_complete(boost::asio::detail::task_io_service_, boost::asio::detail::task_io_service_operation_, boost::system::error_code const&, unsigned long) ()  
  #2  0x0000000000501af5 in boost::asio::detail::task_io_service::run(boost::system::error_code&) ()  
  #3  0x00000000004fd9c4 in boost::asio::io_service::run() [clone .isra.477]()  
  #4  0x00000000004f9866 in main ()

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-28 16:32:12 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-256966824  

Can you please email me the trace? vinnie.falco@gmail.com

---

## Comment 2

> Username: bitbugprime  
> Created at: 2016-10-28 16:36:08 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-256967710  

The trace was above but hard to read, I sent you one by email (but it's from an internal email so you can't reply to it).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-28 16:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-256968148  

I see it, please confirm that it looks like this function is stuck in an infinite loop:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/parse.ipp#L106  
  
Some questions that could help:  
- Can this be reproduced?  
- Do you have the HTTP request or response that causes the loop?  
  
Meanwhile, I am studying the function to see if I can guess what condition is causing a failure to terminate the state machine.

---

## Comment 4

> Username: bitbugprime  
> Created at: 2016-10-28 16:42:40 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-256969239  

Infinite loop is what it looks like to me.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-11-07 21:46:40 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-258973116  

Has this been resolved? Is there any additional information?

---

## Comment 6

> Username: bitbugprime  
> Created at: 2016-11-07 22:58:52 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-258990988  

Appears to be resolved in b18.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-11-07 23:03:29 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-258991997  

Do we have any idea which change resolved it? I can create a new issue to investigate it further to make sure it doesn't pop up again in the future.

---

## Comment 8

> Username: bitbugprime  
> Created at: 2016-11-08 05:48:47 UTC  
> Updated at: 2016-11-08 05:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-259051627  

Yes, this change in include/beast/http/impl/parse.ipp:  
  
```  
                 return;  
              }  
              if(used > 0)  
 -                d.started = true;  
 -            d.db.consume(used);  
 +            {  
 +                d.got_some = true;  
 +                d.db.consume(used);  
 +            }  
              if(d.p.complete())  
              {  
                  // call handler  
  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-11-08 12:04:01 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-259119606  

Great, thanks!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-11-08 12:15:07 UTC  
> Url: https://github.com/boostorg/beast/issues/159#issuecomment-259121602  

Addressed by #175
