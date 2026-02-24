# #1073 - Race Condition in Advanced Server Example [Closed]

> Username: cmazakas  
> Created at: 2018-03-18 21:40:53 UTC  
> Updated at: 2018-04-24 01:19:57 UTC  
> Closed at: 2018-04-24 01:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1073  

In the example found at : http://www.boost.org/doc/libs/1_66_0/libs/beast/example/advanced/server/advanced_server.cpp  
  
Given the lines of code:  
```cpp  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // Run the timer. The timer is operated  
        // continuously, this simplifies the code.  
        on_timer({});  
  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Set the timer  
        timer_.expires_after(std::chrono::seconds(15));  
  
        // Read a request  
        http::async_read(socket_, buffer_, req_,  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &http_session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
  
    // Called when the timer expires.  
    void  
    on_timer(boost::system::error_code ec)  
    {  
        if(ec && ec != boost::asio::error::operation_aborted)  
            return fail(ec, "timer");  
  
        // Verify that the timer really expired since the deadline may have moved.  
        if(timer_.expiry() <= std::chrono::steady_clock::now())  
        {  
            // Closing the socket cancels all outstanding operations. They  
            // will complete with boost::asio::error::operation_aborted  
            socket_.shutdown(tcp::socket::shutdown_both, ec);  
            socket_.close(ec);  
            return;  
        }  
  
        // Wait on the timer  
        timer_.async_wait(  
            boost::asio::bind_executor(  
                strand_,  
                std::bind(  
                    &http_session::on_timer,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
```  
The invocation of each callback is serialized properly by the strand. However, the initiating call is not stranded allowing for the possibility of the initial `run` call to happen the same time the strand begins executing its registered callbacks.  
  
See:  
```cpp  
            // Create the http_session and run it  
            std::make_shared<http_session>(  
                std::move(socket_),  
                doc_root_)->run();  
```  
  
To remedy, the initial `run()` call must be done on the same strand. This will guarantee proper serialized execution of handlers.
