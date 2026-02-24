# #940 - Request for termination, cache implementation examples [Closed]

> Username: cos-public  
> Created at: 2017-12-16 04:46:58 UTC  
> Updated at: 2018-01-01 21:52:29 UTC  
> Closed at: 2018-01-01 21:52:29 UTC  
> Url: https://github.com/boostorg/beast/issues/940  

Having fun with your library for a few days. However a bit overwhelmed by abstractions and customization abilities). A few suggestions would be helpful:  
1. How to gracefully shutdown a server. I'm using following approach (http_server_coro_ssl.cpp example, all the code encapsulated into the Server class):  
```  
class Server {  
public:  
   Server(...) {  
      //do initialization stuff from http_server_coro_ssl.cpp main()  
   }  
   ~Server() {  
      // Is this correct? Do I need to close active sessions? Can I close sockets from other (main) thread?  
      m_ioc.stop();  
      for (auto & t : m_threads)  
	t.join();  
   }  
protected:  
   //do_listen(), do_session(), handle_request() handlers  
private:  
  std::vector<std::thread> m_threads;  
  boost::asio::io_context m_ioc;  
  ssl::context m_ctx;  
}  
```  
  
  
2. How to properly terminate a server with fatal exception. Currently I came to the following solution (modified http_server_coro_ssl.cpp example):  
```  
static std::promise<void> terminate_promise;  
static std::future<void> terminate_future = terminate_promise.get_future();  
void do_listen(...) {  
    try {  
        //trying to listen  
    } catch (...) {  
      terminate_promise.set_exception(std::current_exception());  
   }  
}  
  
void sig_term_handler() {  
   terminate_promise.set_value();  
}  
  
int main(int argc, char* argv[]) {  
  try {  
    // create context, load certificates etc.  
  
    std::vector<std::thread> v;  
    v.reserve(threads);  
    for(auto i = threads - 1; i >= 0; --i)  
        v.emplace_back(  
        [&ioc]  
        {  
            ioc.run();  
        });  
    // ioc.run(); // NOT launching io on current thread  
    terminate_future.get();  
    return EXIT_SUCCESS;  
  } catch (...) {  
      std::cerr << "terminate with exception";  
      return EXIT_FAILURE;  
  }  
}  
```  
3. Is there any reasoning behind using error_codes instead of exceptions in examples (in http_server_coro_ssl.cpp do_session(), do_listen() specifically) ?  
  
4. What is the best place to implement a cache. I would like to prepare an object, which will be the most ready for sending over network. I have all the data, which will not change (neither headers nor body). Memory space is not a concern.  
  
5. Excuse my ignorance in HTTP details, but do I need to activate deflation explicitly? I assume I need to use /boost/beast/zlib/deflate_stream.hpp. Is there any example? Can I cache deflated response?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-16 13:34:36 UTC  
> Updated at: 2017-12-16 13:35:28 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352183929  

>How to gracefully shutdown a server  
  
Well that depends on how you define "graceful." A truly graceful shutdown would work as follows:  
  
1. The listening socket is first closed, to prevent new connections  
2. Responses already being sent are allowed to continue  
3. Any new requests get an HTTP 503 Service Unavailable response with "Connection: close" (and the connection is closed afterwards)  
4. Any idle connections are closed after 15 seconds  
5. After all connections are closed the program exits normally, invoking destructors  
  
This is a real pain to implement especially when the server is multi-threaded, which is why I have not included it in the examples.  
  
>Is this correct? Do I need to close active sessions? Can I close sockets from other (main) thread?  
  
The code you provided will not work correctly. You need to first close the listening socket (to prevent new sessions) then loop over each session and call `cancel` on the socket. Asio sockets are not thread-safe, so you need to make the call to `cancel` from the strand using `boost::asio::post`:  
```  
// Without strand  
boost::asio::post(  
    socket_.get_executor(),  
    [&]()  
    {  
        socket_.cancel();  
    });  
  
// With strand  
boost::asio::post(  
    socket_.get_executor(),  
    boost::asio::bind_executor(  
        strand_,  
        [&]()  
        {  
            socket_.cancel();  
        }));  
```  
  
After you call `cancel` on each socket, then you can just `join` all the threads. You do not want or need to call `stop` on the `io_service`. Pending I/O operations will complete with `error::operation_aborted` and lead to the destruction of the session. When all the sessions are destroyed the threads will exit, since there is no more pending work. This is not the most graceful shutdown, existing connections would simply be closed even if in the middle of a message. But it works.  
  
>How to properly terminate a server with fatal exception. Currently I came to the following solution (modified http_server_coro_ssl.cpp example):  
  
I do not understand what you are trying to do there. You want to handle exceptions? You want to cause the app to terminate by throwing an exception?  
  
>Is there any reasoning behind using error_codes instead of exceptions in examples (in http_server_coro_ssl.cpp do_session(), do_listen() specifically) ?  
  
The examples are meant to illustrate a variety of uses, so I have included examples with use exceptions simply to show that it is possible. Personally I prefer to use error codes since they work better with asynchronous code.  
  
>What is the best place to implement a cache. I would like to prepare an object, which will be the most ready for sending over network. I have all the data, which will not change (neither headers nor body). Memory space is not a concern.  
  
The best "place" to implement a cache, or anything, is in your code. I don't understand what you're asking here.  
  
>Excuse my ignorance in HTTP details, but do I need to activate deflation explicitly? I assume I need to use /boost/beast/zlib/deflate_stream.hpp. Is there any example? Can I cache deflated response?  
  
Beast only handles the chunked Transfer-Encoding, and then only when it appears last in the list of encodings specified in the "Transfer-Encoding" field value. Anything else is up to you. You can use deflate_stream.hpp but note that it does not handle the gzip wrapper (this will be added in a future version of beast). Yes you should be able to cache compressed content bodies.

---

## Comment 2

> Username: cos-public  
> Created at: 2017-12-16 14:53:45 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352188255  

Thank you very much for detailed answers!  
  
>> How to gracefully shutdown a server  
  
> Well that depends on how you define "graceful." A truly graceful shutdown would work as follows:  
  
Yes. That is exactly what I meant. Very informative instructions.  
  
  
>>How to properly terminate a server with fatal exception. Currently I came to the following solution (modified http_server_coro_ssl.cpp example):  
  
>I do not understand what you are trying to do there  
  
I was asking about passing notifications from inside the server (threads) to notify the main program to close the server. Knowing the way of terminating the server somewhat clarifies this question also.  
  
  
>> Is there any reasoning behind using error_codes instead of exceptions in examples (in http_server_coro_ssl.cpp do_session(), do_listen() specifically) ?  
  
> Personally I prefer to use error codes since they work better with asynchronous code.  
  
It would be helpful to know the benefits of using error codes and caveats of using exceptions in listen() and accept() coroutines to not face any difficulties in future.  
  
  
>> What is the best place to implement a cache.   
  
> The best "place" to implement a cache, or anything, is in your code. I don't understand what you're asking here.  
  
Basically I was asking whether it's possible or makes sense to store serialized response (body + headers) or deflated body. I will go with serialized [deflated] body.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-16 17:17:11 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352196874  

>I was asking about passing notifications from inside the server (threads) to notify the main program to close the server. Knowing the way of terminating the server somewhat clarifies this question also.  
  
You don't need to notify the main program to close the server. Instead, just take steps to make `io_context::run` return. And make sure that your main thread is calling `io_context::run`. If you close the listening socket and let all connections be destroyed, then there will be no more work remaining and your program will just naturally terminate.  
  
>It would be helpful to know the benefits of using error codes and caveats of using exceptions in listen() and accept() coroutines to not face any difficulties in future.  
  
It all depends on your style. I find errors easier to code. Some people like exceptions though. I think exceptions are better in client code. But error codes are better in servers.  
  
>Basically I was asking whether it's possible or makes sense to store serialized response (body + headers) or deflated body. I will go with serialized [deflated] body.  
  
If you can store a complete `message` with the headers already set, that would be best since you can avoid allocating and setting the headers on every response. Otherwise, caching the serialized body is also a good choice. You can create your own **Body** type, for example to implement a "shared body" (one that can be attached to multiple messages). The documentation explains how to do this:  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http/custom_body_types.html  
  
This video also helps:  
  
https://www.youtube.com/watch?v=WsUnnYEKPnI

---

## Comment 4

> Username: cos-public  
> Created at: 2017-12-16 19:43:32 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352206463  

> You don't need to notify the main program to close the server... make sure that your main thread is calling io_context::run  
  
If I want to integrate a web server functionality into the system, I would like to leave main thread to do other stuff and shutdown server independently from other parts without terminating the whole application. But I think I have enough information to terminate it correctly now.  
  
> It all depends on your style.  
  
If I can just switch to exceptions and there are no edge cases in handling them, then... it's great.  
  
> If you can store a complete message with the headers already set, that would be best since you can avoid allocating and setting the headers on every response  
  
Yes, that's what my initial question was about and it's great that both options are possible.  
  
> This video also helps: https://www.youtube.com/watch?v=WsUnnYEKPnI  
  
I already watched the video. It was very helpful and the most important and useful aspect for me was the reasoning behind the abstractions.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-17 00:44:26 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352222734  

>If I can just switch to exceptions and there are no edge cases in handling them, then... it's great.  
  
Well if you are using asynchronous I/O the errors are always delivered to the callback via `error_code` so you have less choice there.

---

## Comment 6

> Username: djarek  
> Created at: 2017-12-17 01:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352224530  

> If I can just switch to exceptions and there are no edge cases in handling them, then... it's great.  
  
Unfortunately, composed asynchronous operations in both Beast and ASIO currently only provide a Basic Exception Guarantee. This means that if an exception is thrown and it interrupts such an operation, then it will never complete and the top level context has no way of determining (unless the exception has the information), which connection the exception was related to and might not be able to clean up properly (assuming you want to eat the exception, close the connection and continue running).  
  
It should be noted, though, that as long as you use the Beast provided async operations and DynamicBuffers, then it's much harder for you to run into this issue, because at the point your Handler throws, the operation is already completed anyway and the contract of the async operation has been fulfilled by then.   
  
> How to gracefully shutdown a server. I'm using following approach (http_server_coro_ssl.cpp example, all the code encapsulated into the Server class)  
  
While this is not graceful at all, you can terminate your server by calling `stop()` on the `io_context` and then joining all the threads. Once the `io_context` goes out of scope, its destructor will destroy all coroutines (but in this case, your code must gracefully handle stackful coroutine forced unwind), which will result in all resources being cleaned up (assuming your code conforms to the RAII idiom and ownership of all connection related resources is retained on the coroutine stack).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-12-17 05:05:29 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-352232586  

>While this is not graceful at all, you can terminate your server by calling stop() on the io_context and then joining all the threads.  
  
Are you sure about that? What happens to sockets with pending I/O?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-12-31 19:22:30 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-354620033  

@djarek ping

---

## Comment 9

> Username: djarek  
> Created at: 2018-01-01 20:55:03 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-354675789  

@vinniefalco   
If there's a pending I/O operation, the CompletionHandler will be destroyed (without being executed) within the platform specific reactor's `shutdown()` function in the io_context's destructor (this implies that the CompletionHandler destructor must destroy the socket, because an I/O object must not outlive its associated `io_context`).

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-01 21:52:29 UTC  
> Url: https://github.com/boostorg/beast/issues/940#issuecomment-354679871  

This opens up new possibilities for implementing timeouts and graceful shutdowns, thanks! I'll go ahead and close this, feel free to open a new issue if you need additional help.
