# #1507 - Http server with websocket server on same ip with 2 different port [Closed]

> Username: Ludea  
> Created at: 2019-03-07 06:46:31 UTC  
> Updated at: 2019-03-07 17:24:06 UTC  
> Closed at: 2019-03-07 17:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1507  

Hello,   
  
I try to setup a websocket server (from https://www.boost.org/doc/libs/1_69_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp) and add an http server (from https://www.boost.org/doc/libs/1_69_0/libs/beast/example/http/server/async/http_server_async.cpp)  
The http is an api endpoint.   
I need to start them into localhost, on different port.   
```  
tcp::endpoint endpoint(tcp::v4(), 4000);  
                tcp::endpoint apiEndpoint(tcp::v4(), 7000);  
                tcp_server server(ioc, endpoint);  
                listener api(ioc, apiEndpoint, doc_root);  
```  
The websocket start ( on 5000 port) but not the http server (on 8080 port).   
  
I try to add some options :  
  
```  
acceptor.set_option(boost::asio::socket_base::reuse_address(true), ec);  
        if(ec)  
        {  
            std::cout<< "set_option : "<< ec.message()<<std::endl;  
            return;  
        }  
```  
 on both websocket and http server, I still can not start both in same time.  
Any idea why?

---

## Comment 1

> Username: djarek  
> Created at: 2019-03-07 09:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1507#issuecomment-470453240  

Perhaps you already have something running on port 8080?

---

## Comment 2

> Username: Ludea  
> Created at: 2019-03-07 12:12:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1507#issuecomment-470502904  

I try some ports, like 7000, and nothing change.  
I also use netstat to verify the open ports.   
No applications use http server port

---

## Comment 3

> Username: Ludea  
> Created at: 2019-03-07 16:32:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1507#issuecomment-470596553  

It seems an issue port.  
I open 2 port, 1 for websocket, 1 for http server.   
The http port is 6000, but, when I do `netstat - tuplen` i see an another port (38882).  
If I reboot the http server, a random port is open.   
I specify port 6000 but random port is assigned.   
Is it a bug?

---

## Comment 4

> Username: djarek  
> Created at: 2019-03-07 17:05:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1507#issuecomment-470610110  

It's likely a bug in your code. Note, that if an acceptor is assigned port `0` it will listen on a random port.   
BTW: This isn't a Beast-related issue, but rather an ASIO-related one.

---

## Comment 5

> Username: Ludea  
> Created at: 2019-03-07 17:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1507#issuecomment-470617147  

Yes, it is not a beast issue.  
I will investigate on the random port
