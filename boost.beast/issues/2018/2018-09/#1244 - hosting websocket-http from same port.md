# #1244 - hosting websocket/http from same port [Closed]

> Username: nazilliefesi  
> Created at: 2018-09-14 06:00:13 UTC  
> Updated at: 2018-09-14 11:16:20 UTC  
> Closed at: 2018-09-14 11:16:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1244  

I have a working http(s) server solution which has been developed directly with boost asio. One of my collegues also have recently developed a websocket library using beast which we use for hosting some services in the same server application.   
  
I have been planning (and already started development to some extent) to use beast for both http server and websocket server functionalities, merge two of our libraries and maybe host http/ws on the same port etc.   
  
I have stumbled upon the following link   
https://www.reddit.com/r/cpp/comments/6in9e9/multi_port_http_https_ws_wss_websocket_openssl/#bottom-comments  
and soon after that I've seen the following comment  
https://github.com/boostorg/beast/issues/946#issuecomment-353678615  
  
Since it has similarities to what I want to achieve, I would be glad to hear the reason(s) for abandoning this example. And I also would like to get your recommendations/warnings how I should and should not use beast for such a scenario if any.  
  
Thanks for good work that has been put into this library

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-14 11:10:48 UTC  
> Updated at: 2018-09-14 11:11:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1244#issuecomment-421325967  

Thanks for the kind words! Both of the advanced server examples do HTTP and WebSocket on the same port. The "advanced-server-flex" program also supports plain and SSL on the same port:  
  
https://github.com/boostorg/beast/tree/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/advanced  
  
I abandoned that particular example because the HTTP router was over-engineered and in my opinion not up to the same level of quality as other code in Beast.

---

## Comment 2

> Username: nazilliefesi  
> Created at: 2018-09-14 11:16:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1244#issuecomment-421327682  

Oh great then. That means its just a change in the way you distribute your examples and beast continues to support this approach.  
  
Thanks & Regards
