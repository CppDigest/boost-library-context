# #286 - boost::asio::deadline_timer cancel() method is not calling timer handler [Closed]

> Username: FerenczD  
> Created at: 2019-09-13 19:16:27 UTC  
> Updated at: 2020-12-30 01:09:34 UTC  
> Closed at: 2020-12-30 01:09:33 UTC  
> Url: https://github.com/boostorg/asio/issues/286  

**Boost 1.69**  
Hi. I'm trying to make a class to do https that does requests but also has a timeout for error checking.   
  
I'm using the boost beast functions for the https (which works fine) and Im using the deadline_timer class to set the timer  
  
I set up the https request and the timer to run on the the same IO_context. The idea is that if the timeout happens I want to throw an error and stop execution of the IO context or if the https works correctly I want to stop the timer.  
  
I'm trying to use the `cancel()` method to stop the timer and invoke the timeout handle as per documentation states   
  
> This function forces the completion of any pending asynchronous wait operations against the timer. The handler for each cancelled operation will be invoked with the boost::asio::error::operation_aborted error code  
  
However the timer handler is never called. How do I do to call the timer handler when I cancel the timer?  
  
Here is some code:  
  
```  
    /* Set up timeout timer and start it */  
    boost::asio::deadline_timer timer{query->ioc,boost::posix_time::seconds(TIMEOUT_HTTPS)};  
    timer.async_wait(std::bind(&HttpsCom::on_timeout, this, std::placeholders::_1, query));  
  
    // Look up the domain name  
    std::cout << "Making the request..." << std::endl;  
    tcp::resolver resolver{query->ioc};  
    resolver.async_resolve(host, port, std::bind(&HttpsCom::on_resolve,this,std::placeholders::_1,std::placeholders::_2, query));  
  
    query->ioc.run();  
  
 if (latestError == HTTPS_SUCCESS){  
      std::size_t test = timer.cancel();  
      std::cout << test << std::endl; // Timer gets cancel as test = 1   
}  
```  
This is my timer handler  
```  
void HttpsCom::on_timeout(const boost::system::error_code& ec, std::shared_ptr<HTTPSQueryStruct_t> query){  
  std::cout << "Timeout" << std::endl;  
  }  
  
```  
  
And this is my shutdown handler (HTTPS was succesful)  
  
```  
void HttpsCom::on_shutdown(boost::system::error_code ec, std::shared_ptr<HTTPSQueryStruct_t> query){  
  std::cout << "on_shutdown()" << std::endl;  
  
  // boost::ignore_unused(ec);  
  std::cerr << "on_shutdown" << ": " << ec.message() << std::endl;      
  
  /* If we get here the connection was closed gracefully */  
  setLatestErrorCode(HTTPS_SUCCESS);  
  query->ioc.stop();  
}  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:09:32 UTC  
> Url: https://github.com/boostorg/asio/issues/286#issuecomment-752292950  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#720](https://github.com/chriskohlhoff/asio/issues/720).
