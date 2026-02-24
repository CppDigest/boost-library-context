# #1701 - Continuos HTTPS requests with single object [Closed]

> Username: FerenczD  
> Created at: 2019-09-11 19:54:38 UTC  
> Updated at: 2019-09-13 20:48:46 UTC  
> Closed at: 2019-09-13 20:48:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1701  

Hi. I've been struggling a bit with this and need some insight on what I can be doing wrong.  
  
A bit of background. I am building a class that can do HTTPS GET/POST by using boost beast. Moreover the class must have timeouts for error checking (I'm using `boost::asio::deadline_timer for timeouts` ).  
  
The logic of the program basically creates a HttpsCom class which calls a POST request to certain url to retreieve an authorization token. After the authorization token is retrieved I want to make a GET request using the same to same object with same host but different target.  
  
Because some variables are required for the async calls I'm storing them as privates in my class definition (Im not sure if thats the correct way to do it).  
  
So this is my class:   
  
```  
class HttpsCom {  
public:  
  const https_Error post(const std::string host, const std::string target, const std::string body_in, const http_headers_t& headers_in,  
                         http_headers_t& headers_out, std::string& body_out);  
  const https_Error get(const std::string host, const std::string target, const std::string body_in, const http_headers_t& headers_in,  
                        http_headers_t& headers_out, std::string& body_out);   
  
  
private:  
  std::string token;  
  
  boost::asio::io_context ioc;  
  ssl::context ctx{ssl::context::sslv23_client};  
  ssl::stream<tcp::socket> stream{ioc, ctx};  
  
  void on_resolve(boost::system::error_code ec,tcp::resolver::results_type results);  
  void on_connect(boost::system::error_code ec);  
  void on_handshake(boost::system::error_code ec);  
  void on_write(boost::system::error_code ec, std::size_t bytes_transferred);  
  void on_read(boost::system::error_code ec, std::size_t bytes_transferred);  
  void on_shutdown(boost::system::error_code ec);  
  void on_timeout();  
```  
  
Post and get are basically the same function just changed a couple params  
```  
HttpsCom::post(){  
/* Set request */  
    this->setRequest(body_in, headers_in, host, target, 0); /* 0 - POST */  
                                                            /* 1 - GET*/  
      
    // Set SNI Hostname. Permits identify right certificate from server  
    if (!SSL_set_tlsext_host_name(stream.native_handle(), host.c_str()))  
    {  
      boost::system::error_code ec{static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category()};  
      throw boost::system::system_error{ec};  
    }  
  
    /* Set up timeout timer and start it */  
    boost::asio::deadline_timer timer{ioc,boost::posix_time::seconds(TIMEOUT_HTTPS)};  
    timer.async_wait(std::bind(&HttpsCom::on_timeout, this));  
  
    // Look up the domain name  
    std::cout << "Making the request..." << std::endl;  
    tcp::resolver resolver{ioc};  
    resolver.async_resolve(host, port, std::bind(&HttpsCom::on_resolve,this,std::placeholders::_1,std::placeholders::_2));  
    try{  
      ioc.restart();  
      ioc.run();    
    }  
    catch (std::exception const &e){  
      std::cerr << "Error: " << e.what() << std::endl;  
      return HTTPS_ERROR;  
    }  
  
    this->parseResponse(body_out, headers_out);  
```  
  
and then the async functions follow this example https://www.boost.org/doc/libs/1_69_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp with the excemption that when either on_read() or on_timeout() finishes I do ioc.stop() to stop handlers to run and I parse my HTTP response if there was no timeout.  
  
My problem is that if I stop my IO_service and I try running it again it does not call the async functions, just skips them. Is there a way to delete all handlers to be run and reset the IO_service?  
I tried using ioc.restart() but it will call the timeout handler even though I got my HTTP response.  
  
I'm using boost 1.69 and cannot use the timeouts from 1.71.  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-11 19:55:41 UTC  
> Updated at: 2019-09-11 19:56:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530540346  

Okay, but what is the question?  
  
Also, have a look at this:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_io/timeouts.html

---

## Comment 2

> Username: FerenczD  
> Created at: 2019-09-11 19:56:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530540611  

Sorry. I submitted the question by accident and I am editing it for completion.

---

## Comment 3

> Username: FerenczD  
> Created at: 2019-09-11 20:08:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530544786  

@vinniefalco I have updated my question. I hope is clear. Let me know what you think.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-09-11 20:09:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530544988  

This sounds very much like an Asio-specific question

---

## Comment 5

> Username: FerenczD  
> Created at: 2019-09-11 20:10:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530545465  

Can you point me out where is a better place to ask this question?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-11 20:10:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530545565  

https://github.com/boostorg/asio/issues ?

---

## Comment 7

> Username: FerenczD  
> Created at: 2019-09-11 20:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1701#issuecomment-530546461  

Ok. Thanks
