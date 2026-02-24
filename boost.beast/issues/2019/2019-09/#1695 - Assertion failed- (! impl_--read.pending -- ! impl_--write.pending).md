# #1695 - Assertion failed: (! impl_->read.pending || ! impl_->write.pending) [Closed]

> Username: benstadin  
> Created at: 2019-09-07 01:55:25 UTC  
> Updated at: 2019-09-08 00:28:09 UTC  
> Closed at: 2019-09-08 00:28:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1695  

I tried to benchmark my server application and got a crash when running wrk at ioc->run():  
  
The crash occurs with Beast 1.70 and 1.71.   
  
`Assertion failed: (! impl_->read.pending || ! impl_->write.pending), function expires_after, file /usr/local/include/boost/beast/core/impl/basic_stream.hpp, line 735.`  
  
The server startup looks like this:  
  
```  
bool WebServer::start() {  
    if (_started) {  
        return false;  
    }  
      
    boost::asio::ip::address asioAddress;  
    try {  
        asioAddress = net::ip::make_address(_address);  
    } catch (...) {  
        std::cerr << "error: invalid host " << _address << std::endl;  
        return false;  
    }  
      
    auto const sharedDocRoot = std::make_shared<std::string>(_docRoot);  
    auto const threads = std::max<int>(1, _threads);  
      
    // The io_context is required for all I/O  
    _ioc = std::make_unique<net::io_context>(threads);  
      
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12};  
      
    // This holds the self-signed certificate used by the server  
    load_server_certificate(ctx);  
      
    // Create and launch a listening port  
    auto listenerInstance = std::make_shared<Listener>(  
                                                       *_ioc,  
                                                       ctx,  
                                                       tcp::endpoint{asioAddress, _port},  
                                                       sharedDocRoot);  
      
    if (!listenerInstance->isValid()) {  
        std::cerr << "error: failed to start HTTP listener" << std::endl;  
        return false;  
    }  
      
    listenerInstance->run();  
      
    // Capture SIGINT and SIGTERM to perform a clean shutdown  
    net::signal_set signals(*_ioc, SIGINT, SIGTERM);  
    signals.async_wait(  
                       [&](beast::error_code const&, int)  
                       {  
                           // Stop the `io_context`. This will cause `run()`  
                           // to return immediately, eventually destroying the  
                           // `io_context` and all of the sockets in it.  
                           _ioc->stop();  
                       });  
      
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for(auto i = threads - 1; i > 0; --i)  
        v.emplace_back(  
                       [&]  
                       {  
                           _ioc->run(); // crash here  
                       });  
      
    _started = true;  
      
    _ioc->run();  
      
    // (If we get here, it means we got a SIGINT or SIGTERM)  
          
    // Block until all the threads exit  
    for(auto& t : v)  
        t.join();  
      
    _ioc = nullptr;  
      
    return true;  
}  
```  
  
Command to run the script:  
wrk -s post.lua -t2 -c400 -d30s http://127.0.0.1:8081/providers/locations  
  
The post.lua wrk script is this:  
  
```  
-- example HTTP POST script which demonstrates setting the  
-- HTTP method, body, and adding a header  
  
wrk.method = "POST"  
wrk.body   = "foo=bar&baz=quux"  
wrk.headers["Content-Type"] = "application/x-www-form-urlencoded"  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-07 01:58:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529062029  

Yes well the code comment at that location provides an insight:  
```  
    // If assert goes off, it means that there are  
    // already read or write (or connect) operations  
    // outstanding, so there is nothing to apply  
    // the expiration time to!  
```  
  
The assert is there to warn you if you are using `expires_after` incorrectly. You can only call that function immediately before performing an asynchronous read or a write.

---

## Comment 2

> Username: benstadin  
> Created at: 2019-09-07 02:05:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529062506  

Hm, my do_read looks as follows (similar to http_server_flex.cpp):  
  
 ```  
       void doRead() {  
            // Construct a new parser for each message  
            _parser.emplace();  
              
            // Apply a reasonable limit to the allowed size  
            // of the body in bytes to prevent abuse.  
            _parser->body_limit(10000);  
              
            // Set the timeout.  
//            beast::get_lowest_layer(derived().stream()).expires_after(std::chrono::seconds(30));  
              
            // Read a request using the parser-oriented interface  
            http::async_read(derived().stream(),  
                             _buffer,  
                             *_parser,  
                             beast::bind_front_handler(&HttpSession::onRead,  
                                                       derived().shared_from_this()));  
        }  
```  
  
When I comment out setting the epiration it crashs on async_read with:  
  
`Assertion failed: (! b_), function pending_guard, file /usr/local/include/boost/beast/core/detail/stream_base.hpp, line 73.`

---

## Comment 3

> Username: benstadin  
> Created at: 2019-09-07 02:23:22 UTC  
> Updated at: 2019-09-07 02:23:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529063732  

I'm only guessing now as I have to look further later, I think it might be related to the queuing code I reuse from advanced_server example. I plugged the server_flex and advanced server examples together to get http+websocket with ssl support.  
  
I do not see this issue when making single requests.

---

## Comment 4

> Username: benstadin  
> Created at: 2019-09-07 02:41:45 UTC  
> Updated at: 2019-09-07 02:46:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529064876  

When I do not directly call the next do_read in on_read it no longer, but what's the actual issue? It's of course slow also:  
  
```  
        void onRead(beast::error_code ec, std::size_t bytesTransferred) {  
            boost::ignore_unused(bytesTransferred);  
              
            // This means they closed the connection  
            if (ec == http::error::end_of_stream)  
                return derived().do_eof();  
              
            if (ec)  
                return fail(ec, "read");  
              
            // See if it is a WebSocket Upgrade  
            if (websocket::is_upgrade(_parser->get())) {  
                // Disable the timeout.  
                // The websocket::stream uses its own timeout settings.  
                beast::get_lowest_layer(derived().stream()).expires_never();  
                  
                // Create a websocket session, transferring ownership  
                // of both the socket and the HTTP request.  
                return makeWebsocketSession(derived().release_stream(),  
                                              _parser->release(),  
                                              _mediator);  
            }  
              
            // Send the response  
            _routeHandler.handleRequest(*_docRoot, _parser->release(), _queue);  
              
            // If we aren't at the queue limit, try to pipeline another request  
            if (! _queue.isFull())  
                beast::bind_front_handler(&HttpSession::doRead,  
                                          derived().shared_from_this()); // instead of calling doRead directly  
                  
        }  
```  
  
Edit: The issue does not appear with GET requests.

---

## Comment 5

> Username: benstadin  
> Created at: 2019-09-07 19:08:42 UTC  
> Updated at: 2019-09-07 19:09:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529138040  

The problem was that I forgot a return after a call to send and in a rather large router table called send twice accidentally, like:  
  
```  
sendSomething(...);  
...  
send(_defaultController.prepareNotFound<Body, Allocator>(req, path));  
```  
  
Does it make sense to assert such user error earlier in Beast, e.g. when calling send after the headers have already been sent?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-07 19:43:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1695#issuecomment-529141202  

> Does it make sense to assert such user error earlier in Beast, e.g. when calling send after the headers have already been sent?  
  
Perhaps it does make sense, but since Beast HTTP algorithms are not stateful there is no way to accomplish that.
