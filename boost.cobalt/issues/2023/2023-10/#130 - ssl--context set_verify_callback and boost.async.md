# #130 - ssl::context set_verify_callback and boost.async [Closed]

> Username: matthijs  
> Created at: 2023-10-09 19:52:01 UTC  
> Updated at: 2023-10-13 19:58:06 UTC  
> Closed at: 2023-10-13 19:58:06 UTC  
> Url: https://github.com/boostorg/cobalt/issues/130  

Hi,  
  
I am using boost.async to setup an websocket connection. Due to requirements I need to send an OCSP request to the CA to verify if the certificate is still valid.  
  
To handle this I'll use the set_verify_callback function in ssl::context:  
```c++  
ctx.set_verify_mode(boost::asio::ssl::verify_peer);  
ctx.set_verify_callback(  
      [](bool preverified, boost::asio::ssl::verify_context& ctx)  
      {  
        // Return when not properly preverified.  
        if (!preverified)  
        {  
          fmt::print(stderr, "preverification failed...\n");  
          return preverified;  
        }  
  
       // Do the OCSP request / response (setup the request using openssl and send the request using beast together with async)  
       // How can I use boost.async here to send the request  
       co_await beast::http::async_write(socket, request);  
  
       // Handle the response...  
    });  
```  
Obviously this doesn't work because the callback is not a coroutine. But how can I handle this situation and keep the ability to cancel the request if something goes wrong?  
  
Keep in mind, I've omitted a lot of code here. For sending http requests I am using a slighty modified version of the [example/http.cpp](https://github.com/klemens-morgenstern/async/blob/master/example/http.cpp). The OCSP request/response is working correctly but now I am looking at integrating this into `ssl::context`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-10-10 02:40:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/130#issuecomment-1754235624  

You can't. Common problem with callback-APIs & coros. Since C++20 coros are stackless, you can't switch coros internally.  
  
And believe me, I tried to find solutions here, but of all of them get a bit too recursive to be safe to use.  
  
Besides restructuring the program (e.g. verifying after the handshake is done, but before you do IO), I see four solutions:  
  
1. Make a sync http call in the verify callback (slow but works - depends on your requirements)  
2. use boost.context for stackful coroutines, available in asio through asio::spawn (If you want to use that I can provide some more pointers)  
3. have a dedicated connection for the OSCP that runs on a different thread. Then use that with `asio::use_future`.  
4. Implement your own SSL handshake. But that might be tough I fear.  
  
I would probably start with 1 for development and then do 3.

---

## Comment 2

> Username: matthijs  
> Created at: 2023-10-13 19:58:06 UTC  
> Url: https://github.com/boostorg/cobalt/issues/130#issuecomment-1762130305  

I was already afraid of that.  
  
I am going to restructure the program. That's the better option in this case.  
  
Thank you for the pointers!
