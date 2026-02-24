# #290 - beast::http::async_write leaks the completion handler closure if io_service is stopped [Closed]

> Username: hoditohod  
> Created at: 2017-03-30 14:08:40 UTC  
> Updated at: 2017-03-31 20:09:48 UTC  
> Closed at: 2017-03-31 20:09:48 UTC  
> Url: https://github.com/boostorg/beast/issues/290  

Hi,  
I have a beast http server which does everything asynchronously (accept, read, write). There's a unit test scenario that sends in a request, receives the response, and immediately stops the server's io_service. Depending on the scheduling (which is undeterministic) I observed 2 scenarios:  
- the write completion handler runs before the io_service is stopped, there's no issue in this case  
- the io_service::run() returns before executing the write completion handler, in this case the the closure class of the handler is leaked  
  
The write itself is complete in both cases since the receiver end gets the response, the difference is whether the completion handler gets executed or not. The easiest way to trigger the second scenario is to restrict the program to run on a single core with `taskset 0x01`. I tried to call stop() from the same thread that executes io_service::run() with a post(), but it doesn't help (this is in accordance with ASIO docs stating stop() is thread-safe).  
  
Here's a small program to reproduce the problem on my system:  
  
    #include <string>  
    #include <future>  
    #include <chrono>  
    #include <iostream>  
      
    #include <boost/asio.hpp>  
    #include <beast/core.hpp>  
    #include <beast/http.hpp>  
      
      
    using namespace std::chrono_literals;  
      
      
    int main()  
    {  
        auto refCounter = std::make_shared<int>();  
        std::cout << "Initial refcount: " << refCounter.use_count() << std::endl;  
      
        // io_service scope  
        {  
            boost::asio::io_service ios;  
      
            auto server = std::async(std::launch::async, [&ios, &refCounter] {  
      
                boost::asio::ip::tcp::socket socket(ios);  
                boost::asio::ip::tcp::acceptor acceptor(ios, boost::asio::ip::tcp::endpoint(boost::asio::ip::tcp::v4(), 8080) );  
      
                beast::streambuf streamBuf;  
                beast::http::request<beast::http::string_body> req;  
                beast::http::response<beast::http::string_body> resp;  
      
                acceptor.accept(socket);  
      
                beast::http::read(socket, streamBuf, req);  
                std::cout << "Request body: " << req.body << std::endl;  
      
                resp.body = "World";  
                resp.version = req.version;  
                resp.status = 200;  
      
                beast::http::prepare(resp);  
                beast::http::async_write(socket, resp, [refCounter](boost::system::error_code){  
                    std::cout << "Write complete!" << std::endl;  
                });  
      
                ios.run();  
                std::cout << "Server thread going down" << std::endl;  
            });  
      
            // wait for server startup  
            std::this_thread::sleep_for(100ms);  
      
      
            boost::asio::ip::tcp::resolver resolver(ios);  
            boost::asio::ip::tcp::socket socket(ios);  
      
            beast::streambuf streamBuf;  
            beast::http::request<beast::http::string_body> req;  
            beast::http::response<beast::http::string_body> resp;  
      
            boost::asio::connect(socket, resolver.resolve(boost::asio::ip::tcp::resolver::query{"127.0.0.1", "8080"}));  
      
      
            req.method = "POST";  
            req.url = "/blabla";  
            req.body = "Hello";  
            req.version = 11;  
      
            beast::http::prepare(req);  
            beast::http::write(socket, req);  
            beast::http::read(socket, streamBuf, resp);  
      
            ios.stop();  
            server.get();   // join server thread  
      
            std::cout << "Response body: " << resp.body << std::endl;  
        }  
      
        std::cout << "Final refcount: " << refCounter.use_count() << std::endl;  
        return 0;  
    }  
  
Output for the normal case:  
  
    $ ./beast_bug   
    Initial refcount: 1  
    Request body: Hello  
    Write complete!  
    Server thread going down  
    Response body: World  
    Final refcount: 1  
  
Output for the leak case:  
  
    $ taskset 0x01 ./beast_bug   
    Initial refcount: 1  
    Request body: Hello  
    Server thread going down  
    Response body: World  
    Final refcount: 2  
  
Env: Ubuntu 16.04 64-bit (x86), Boost 1.62, Beast 1.0.0-b31

---

## Comment 1

> Username: harrishancock  
> Created at: 2017-03-31 00:22:19 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290581645  

Good find. I can confirm the leak.  
  
**valgrind output for normal case:**  
```  
$ valgrind ./beast_bug  --leak-check=full  
==9071== Memcheck, a memory error detector  
==9071== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.  
==9071== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info  
==9071== Command: ./beast_bug --leak-check=full  
==9071==   
Initial refcount: 1  
Request body: Hello  
Write complete!  
Server thread going down  
Response body: World  
Final refcount: 1  
==9071==   
==9071== HEAP SUMMARY:  
==9071==     in use at exit: 72,704 bytes in 1 blocks  
==9071==   total heap usage: 45 allocs, 44 frees, 84,686 bytes allocated  
==9071==   
==9071== LEAK SUMMARY:  
==9071==    definitely lost: 0 bytes in 0 blocks  
==9071==    indirectly lost: 0 bytes in 0 blocks  
==9071==      possibly lost: 0 bytes in 0 blocks  
==9071==    still reachable: 72,704 bytes in 1 blocks  
==9071==         suppressed: 0 bytes in 0 blocks  
==9071== Rerun with --leak-check=full to see details of leaked memory  
==9071==   
==9071== For counts of detected and suppressed errors, rerun with: -v  
==9071== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
```  
  
**valgrind output for leak case:**  
```  
$ valgrind ./beast_bug  --leak-check=full  
==9062== Memcheck, a memory error detector  
==9062== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.  
==9062== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info  
==9062== Command: ./beast_bug --leak-check=full  
==9062==   
Initial refcount: 1  
Request body: Hello  
Server thread going down  
Response body: World  
Final refcount: 2  
==9062==   
==9062== HEAP SUMMARY:  
==9062==     in use at exit: 74,009 bytes in 7 blocks  
==9062==   total heap usage: 45 allocs, 38 frees, 84,686 bytes allocated  
==9062==   
==9062== LEAK SUMMARY:  
==9062==    definitely lost: 32 bytes in 1 blocks  
==9062==    indirectly lost: 1,273 bytes in 5 blocks  
==9062==      possibly lost: 0 bytes in 0 blocks  
==9062==    still reachable: 72,704 bytes in 1 blocks  
==9062==         suppressed: 0 bytes in 0 blocks  
==9062== Rerun with --leak-check=full to see details of leaked memory  
==9062==   
==9062== For counts of detected and suppressed errors, rerun with: -v  
==9062== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-03-31 14:00:00 UTC  
> Updated at: 2017-03-31 14:00:29 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290719845  

I believe the `io_service` destructor has to be called to destroy those queued completion handlers. Otherwise, if you call `io_service::stop`, then `io_service::reset`, the completion handlers would not execute thereby breaking invariants.  
  
Nothing in the asio documentation leads me to believe that a call to `io_service::stop` should destroy pending completion handlers:  
http://www.boost.org/doc/libs/1_60_0/doc/html/boost_asio/reference/io_service/stop.html  
http://www.boost.org/doc/libs/1_60_0/doc/html/boost_asio/reference/io_service/_io_service.html

---

## Comment 3

> Username: hoditohod  
> Created at: 2017-03-31 14:13:35 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290723378  

Hi Vinnie,  
Yes, I'm aware of that. That's why io_service is scoped to have its destructor complete before the final refcount is printed. The destructor between "Response body" and "Final refcount" logs should have been destoryed the pending handler.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-03-31 14:14:09 UTC  
> Updated at: 2017-03-31 14:14:37 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290723552  

Hmm, I have written a test that exercises the scenarios you are describing and it confirms my earlier statements:  
https://github.com/vinniefalco/Beast/blob/b32/test/http/read.cpp#L389

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-03-31 14:15:54 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290724022  

I don't have any `taskset` on Windows, is it possible to reproduce your scenario in the style of the test that I have linked above? By just calling `io_service::run_one`, instead of using a separate thread?

---

## Comment 6

> Username: hoditohod  
> Created at: 2017-03-31 14:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290728294  

I couldn't reproduce the problem with http::async_read (I haven't tried much, though), but it was easy with http::async_write. The occurrence rate is very low on multiprocessor system (1 in 1000's in my unit test), but if I restrict the scheduler to use a single core only with `taskset 0x01` the occurrence rate increases to 1 in 3.  
  
The valgrind logs @harrishancock posted also show a leak in some of the executions, they shouldn't be there as the io_service destructor eventually runs before exiting and valgrind accounts for that.  
  
I didn't dig deep into this (I simply didn't get all the template magic), but the leaked allocation happens in this area (stack except):  
  
    #7  0x0000000000c61af3 in beast::handler_ptr<beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, http::HttpSession<http::ZmqClient>::WriteCompleteCallback, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::data, http::HttpSession<http::ZmqClient>::WriteCompleteCallback>::P::P<http::HttpSession<http::ZmqClient>::WriteCompleteCallback const&, boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >&, beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > const&> (this=0x7fffdc00a790, h=...) at handler_ptr.ipp:24  
    #8  0x0000000000c5fc17 in beast::handler_ptr<beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, http::HttpSession<http::ZmqClient>::WriteCompleteCallback, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::data, http::HttpSession<http::ZmqClient>::WriteCompleteCallback>::handler_ptr<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >&, beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > const&> (this=0x7fffeeffc1f0, handler=...) at handler_ptr.ipp:91  
    #9  0x0000000000c5e393 in beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, http::HttpSession<http::ZmqClient>::WriteCompleteCallback, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::write_op<http::HttpSession<http::ZmqClient>::WriteCompleteCallback&, beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > const&> (this=0x7fffeeffc1f0, h=..., s=...) at write.ipp:376  
    #10 0x0000000000c5cd48 in beast::http::async_write<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> >, http::HttpSession<http::ZmqClient>::WriteCompleteCallback>(boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >&, beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > const&, http::HttpSession<http::ZmqClient>::WriteCompleteCallback&&) (stream=..., msg=..., handler=<unknown type in /home/twinsen/Git/gehc-av-broker/target/CMakeBuild/Test/target/bin/test_broker, CU 0x57162b, DIE 0x635fd9>) at write.ipp:732

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-03-31 14:40:15 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290730651  

Aha, I switched over to `async_write` and this happened!  
https://github.com/vinniefalco/Beast/blob/b32/test/http/write.cpp#L653  
```  
beast.http.write  
#42 failed: write.cpp(693)  
77ms, 1 suite, 1 case, 42 tests total, 1 failure  
Detected memory leaks!  
Dumping objects ->  
{10817} normal block at 0x0194C3E8, 1036 bytes long.  
 Data: <<   <       GET > 3C C3 94 01 3C C3 94 01 00 04 00 00 47 45 54 20   
{10813} normal block at 0x01948678, 8 bytes long.  
 Data: <  o h   > 10 A3 6F 01 68 83 94 01   
{10812} normal block at 0x01948448, 8 bytes long.  
 Data: <  o h   > 10 A3 6F 01 68 83 94 01   
{10811} normal block at 0x0194C328, 145 bytes long.  
 Data: <      / \ /   / > 00 CD CD CD D8 F0 2F 01 5C F0 2F 01 B4 F0 2F 01   
{10810} normal block at 0x01948368, 8 bytes long.  
 Data: <(       > 28 C3 94 01 02 00 CD CD   
Object dump complete.  
The program '[5564] http-tests.exe' has exited with code 1 (0x1).  
```

---

## Comment 8

> Username: hoditohod  
> Created at: 2017-03-31 14:57:46 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290735526  

That might be it, but you have a expectation on a zero handler count in your test and I don't see it failing in the posted output... What are using for leak checks?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-03-31 15:01:26 UTC  
> Updated at: 2017-03-31 15:02:46 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290736585  

The failure is right there: `#42 failed: write.cpp(693)`  
I'm using the MSVCRTD runtime (Microsoft Visual Studio Debug C Runtime) which has leak checking built in. This is 100% reproducible on every run, and I think I have narrowed it down to some tom foolery feature which I was planning on removing anyway, this will just hasten its demise. The feature is the **Writer** ability to "suspend" and "resume" the asynchronous write operation, which I think is an unnecessary feature (once I add a little more functionality to Beast's HTTP writing APIs). I believe this code is suspect:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/write.ipp#L380  
  
The handler is putting a copy of itself into a `std::function` which it then owns, creating a cycle. When the handler is invoked, it clears out these copies: https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/write.ipp#L536  
  
But if the handler is destroyed, then the cycle remains! Whoops!... thanks for the great bug report it gave me all the information I needed to make it 100% reproducible. And once I can make something happen consistently it is guaranteed to be fixed!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-03-31 16:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290759572  

I believe this fixes the leak!  
https://github.com/vinniefalco/Beast/commits/b32

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-03-31 17:18:39 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290773878  

If you'd like to participate in the code review for these changes please sign yourself up on the pull request:  
https://github.com/vinniefalco/Beast/pull/294  
Thanks!

---

## Comment 12

> Username: hoditohod  
> Created at: 2017-03-31 17:43:50 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290780079  

I can check in the evening if thats ok.

---

## Comment 13

> Username: hoditohod  
> Created at: 2017-03-31 20:09:36 UTC  
> Url: https://github.com/boostorg/beast/issues/290#issuecomment-290817094  

b32 fixes the issue in my tests too! :)  
Thank you!
