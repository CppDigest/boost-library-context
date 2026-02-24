# #2368 - server side websocket closed after a few seconds with read timeout error [Closed]

> Username: RavikumarTulugu  
> Created at: 2021-12-31 06:28:04 UTC  
> Updated at: 2022-01-04 06:37:42 UTC  
> Closed at: 2022-01-04 06:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2368  

I am developing  a async server and building up on  the async server example. every thing works fine except the read handler on the sever side fires with error "The socket was closed due to a timeout". i am passing the below options to the websocket but it seems the library is not honoring the options and settles to default behavior. I am pasting the code below. Please advise if this is a known issue or some thing wrong with my usage.    
  
My connection is idle most of the time like few mins. i dont want the websocket closed due to this 'idle' timeout.   
is accept handler right place to set these options ??    
  
 void on_accept(beast::error_code ec) {  
    **websocket::stream_base::timeout opt{ std::chrono::seconds(30), websocket::stream_base::none(), false };  
    ws_.set_option(opt);**  
    if(ec) return fail(ec, "ws:accept");  
    do_read();  
    return;  
  }

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-12-31 07:07:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003291086  

Best to set the options immediately after creating the websocket::stream object and prior to calling `async_accept`.  
  
If the websocket is going to be idle for a while, you probably want to enable pings and have some kind of ping timeout. The default options do this.  
Assuming you don't own the client, in the absence of pings it might give up and close its socket.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2021-12-31 16:11:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003409671  

i have moved the options to the constructor right after the creation of websocket. still the same.  the client is a browser which uses websockets rarely for certain communication. i will look at the ping and see if it solves. but i would wait for the authors reply.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-12-31 18:40:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003433682  

Are you able to share a GitHub repo containing your project? I’m happy to take a look. I’m beast’s maintainer.

---

## Comment 4

> Username: RavikumarTulugu  
> Created at: 2022-01-01 05:19:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003508315  

our project is not public, i can paste the parts of our server source code here.

---

## Comment 5

> Username: RavikumarTulugu  
> Created at: 2022-01-01 05:27:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003508845  

here is my session constructor code pasted , i am setting the options right after the creation of websocket. my code is based on this example   
[https://www.boost.org/doc/libs/master/libs/beast/example/websocket/server/async/websocket_server_async.cpp  
](url)  
  
  websocketSession(  
      boost::asio::ip::tcp::socket&& socket  
#ifdef WS_SSL  
      , ssl::context& ctx   
#endif  
      ,std::shared_ptr<gameServerClient> gsclnt = nullptr  
      )     
    : ws_(  
        std::move(socket)  
#ifdef WS_SSL  
        , ctx   
#endif  
        ),    
    ioc_(static_cast<boost::asio::io_context&>(socket.get_executor().context())),  
    _gsclnt ( gsclnt ),  
        pingTimer(ioc_, boost::asio::chrono::seconds(10)) {  
          boost::beast::error_code ec;   
          auto remote = boost::beast::get_lowest_layer(ws_).socket().remote_endpoint(ec);  
          remoteIpAddress = remote.address().to_string();  
          remotePort      = remote.port();  
          pingTimer.async_wait(boost::bind(&websocketSession::pingTimerCallback, this, boost::asio::placeholders::error));  
          _info << "New client connection : IP : " << remoteIpAddress << " port : " << remotePort << std::endl;  
          std::cerr << "New client connection : IP : " << remoteIpAddress << " port : " << remotePort << std::endl;  
          ws_.binary ( true ); //we are using binary sockets.  
          //ws_.set_option(websocket::stream_base::timeout::suggested(beast::role_type::server));  
          ws_.set_option(  
              websocket::stream_base::decorator(  
                [=](http::response_header<> &hdr) {  
                hdr.set( http::field::sec_websocket_protocol, "humblepeer");  
                }));  
          websocket::stream_base::timeout opt{ std::chrono::seconds(30), websocket::stream_base::none(), false };  
          ws_.set_option(opt);  
          return;  
        }

---

## Comment 6

> Username: RavikumarTulugu  
> Created at: 2022-01-01 05:28:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003508928  

I am using boost_1.74 which comes with the distro.

---

## Comment 7

> Username: RavikumarTulugu  
> Created at: 2022-01-03 06:35:18 UTC  
> Updated at: 2022-01-03 06:37:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003895005  

even setting the keep_alive pings and idle timeout didnt help.  This causes server to timeout as well. I am exploring whether i have to implement custom heartbeating myself.   
The client is a firefox web browser in this case.   
changing boost to 1.78 didnt help either.   
  websocket::stream_base::timeout opt{   
            std::chrono::seconds(30),  
              std::chrono::seconds(300),  
              true };  
          ws_.set_option(opt);

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-01-03 06:47:43 UTC  
> Updated at: 2022-01-03 07:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003897658  

I think I’m going to need more information. Can you share a GitHub repo containing:  
  
- A complete working minimal program that exhibits the disconnect behaviour you are seeing:  
- exact instructions to repeat your test results  
  
This should require no more than a page of code, and may help me to understand what is going wrong. At this time, it is more likely to be a logic error in the wider program than a problem with Beast.

---

## Comment 9

> Username: RavikumarTulugu  
> Created at: 2022-01-03 07:47:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003916887  

@madmongo1   
Please find the file uploaded, as already told, i am building up on the async server example in the beast folder.   
also please let me know whether i need to use async_ping api for sending ping or the library automatically takes care with the keepalive option.   
[ws.txt](https://github.com/boostorg/beast/files/7800810/ws.txt)

---

## Comment 10

> Username: madmongo1  
> Created at: 2022-01-03 07:50:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003917752  

Beast automatically takes care of the keepalive provided there is an active async_read in progress on the websocket stream.  
  
to be clear, you are simply compiling the example and navigating to it with a browser?  
Can you share the http content you are loading into the browser so I can repeat the test?

---

## Comment 11

> Username: RavikumarTulugu  
> Created at: 2022-01-03 08:37:00 UTC  
> Updated at: 2022-01-03 08:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1003939143  

I am uploading the browser side code as well, i have renamed the files as txt for github to allow uploading.   
just press the connect button and leave for a few seconds, the server will print the error on its console. it might be a default behavior but can we override this.   
[index.html.txt](https://github.com/boostorg/beast/files/7800967/index.html.txt)  
[script.js.txt](https://github.com/boostorg/beast/files/7800968/script.js.txt)  
[styles.css.txt](https://github.com/boostorg/beast/files/7800969/styles.css.txt)

---

## Comment 12

> Username: RavikumarTulugu  
> Created at: 2022-01-04 06:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2368#issuecomment-1004561082  

I am not seeing the issue on the example code. so closing this. still need to debug my code.
