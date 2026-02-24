# #143 - SSL websocket server [Closed]

> Username: goflatworld  
> Created at: 2016-10-16 03:50:41 UTC  
> Updated at: 2016-10-18 11:23:12 UTC  
> Closed at: 2016-10-16 09:55:00 UTC  
> Url: https://github.com/boostorg/beast/issues/143  

Hi,  
  
I'm trying to use beast with SSL to write a Websocket server. The code is in below URL. It compiles OK but I can not make a HTML5 connection from the html client. The error message on the server side is "uninitialized"  
  
https://github.com/goflatworld/beasttest  
  
The key part is below:  
  
.........................................................................  
  
```  
    typedef beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> beastsslsocket;  
     beastsslsocket wssocket_;  
     boost::asio::streambuf buff_;  
      namespace websocket {  
     namespace applications {  
    namespace chat {  
  
        session::session(boost::asio::io_service& io_service,   
            boost::asio::ssl::context& context)  
            : socket_(io_service,context),   
              io_service_(io_service),  
              strand_(io_service),  
              wssocket_(io_service,context)  
                 {  
                     }  
  
       void session::do_read_header()  
        {  
  
             boost::asio::async_read_until(socket_, buff_, "\r\n\r\n",  
                boost::bind(&session::http_handle_read,shared_from_this(),  
                    boost::asio::placeholders::error));  
  
        }  
  
        void session::http_handle_read(const boost::system::error_code& error)  
        {  
            if (!error)  
            {  
  
                         wssocket_.async_accept(buff_.data(),  
                            strand_.wrap(  
                            boost::bind(&session::http_do_read, shared_from_this(),  
                            boost::asio::placeholders::error)));  
            }  
        }  
```  
  
...........................................................................  
  
Can you please check to see whether I used the correct way to accept the connection or not?   
  
If you want to reproduce the issue, you can:  
1. Compile the code on Ubuntu by running the "compile" script.  
2. If you don't want to generate keys, you can let me  know so that I can send you the server's certificate, key and DH key. Save these keys to /etc/oob/ directory.  
3. Run the html5client.html  
4. Check the server console, you should see the error messages.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-16 04:15:34 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254026484  

The error is coming from this line?  
https://github.com/goflatworld/beasttest/blob/master/session.cpp#L83  
  
Can you please paste the exact output? I can't run the server.

---

## Comment 2

> Username: goflatworld  
> Created at: 2016-10-16 04:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254026651  

You are correct. The error is from that line. The full message is as below:  
  
#   
  
Inside http_do-read, checking wsssocket_.accept result  
uninitialized  
  
#   
  
If you want, I can send you all the server keys so that you can run the server.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-16 04:22:07 UTC  
> Updated at: 2016-10-16 04:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254026670  

It looks like you're reading the initial HTTP Upgrade request into a streambuf here:  
https://github.com/goflatworld/beasttest/blob/master/session.cpp#L50  
  
You don't need to do that, you can just call this overload of `async_accept` which reads the HTTP Upgrade request for you, and sends an appropriate response.  
http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_accept/overload1.html  
  
Your code should work even without this change, I'm still looking at it.  
  
I can only debug on Windows.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-16 04:28:24 UTC  
> Updated at: 2016-10-16 04:29:53 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254026846  

Okay, I see what's happening.  
  
First, you forgot a strand here:  
https://github.com/goflatworld/beasttest/blob/master/session.cpp#L32  
  
Second, you have two sockets in `session`. There's one here:  
https://github.com/goflatworld/beasttest/blob/master/session.hpp#L66  
  
And there's one here:  
https://github.com/goflatworld/beasttest/blob/master/session.hpp#L70  
  
I suggest you remove `socket_`, and change the code thusly:  
  
```  
void session::start()  
{  
    wssocket_.next_layer().async_handshake(  
        boost::asio::ssl::stream_base::server, strand_.wrap(  
            boost::bind(&session::handle_handshake, shared_from_this(),  
                boost::asio::placeholders::error)));  
}  
  
void session::handle_handshake(const boost::system::error_code& error)  
{  
    if (! error)  
    {  
        wssocket_.async_accept(strand_.wrap(  
            boost::bind(&session::do_read, shared_from_this(),  
                boost::asio::placeholders::error)));  
    }  
}  
```

---

## Comment 5

> Username: goflatworld  
> Created at: 2016-10-16 05:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254028631  

Great. Thanks. That worked and I could make Websocket connection to the server now.  
  
One more question:   
For non-websocket client, my understanding is that all I need to do is call wsscoket_.next_layer().xxxxx to perform the normal socket operation?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-16 09:12:51 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254036039  

You said "non-websocket client" but you are referring to `wssocket_`. Is that what you meant? Given this declaration:  
  
```  
beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>> ws;  
```  
- `ws` will have type `beast::websocket::stream<...>`  
- `ws.next_layer()` will have type `boost::asio::ssl::stream<...>`  
- `ws.next_layer().next_layer()` will have type `boost::asio::ip::tcp::socket`  
- `ws.lowest_layer()` will have type `boost::asio::ip::tcp::socket`  
  
Does this help explain things?

---

## Comment 7

> Username: goflatworld  
> Created at: 2016-10-16 09:17:41 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254036232  

Thanks for the info. Yes. I meant to use beast websocket to handle normal socket. I think I have the idea on how to use it now. Do you mind keep this open for a few days so that I can perform test to confirm my understanding?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-10-16 09:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254036340  

An issue can still be accessed after it is closed (for example, see #102).

---

## Comment 9

> Username: goflatworld  
> Created at: 2016-10-16 09:54:16 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254037668  

Thanks. I have tested the beast websocket next_layer(). I can handle normal socket as well as websocket using it. I think I finally get it. Thanks for your help again.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-10-16 09:55:00 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254037703  

Glad to help!

---

## Comment 11

> Username: goflatworld  
> Created at: 2016-10-17 10:09:05 UTC  
> Updated at: 2016-10-17 10:10:42 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254166991  

I'm having new issues when send packet to client:  
  
Code is in same URL: https://github.com/goflatworld/beasttest/blob/master/session.cpp:  
  
The key code is as below:  
  
#   
  
```  
  std::string msg="Hello World";  
  wssocket_.async_write(  
                    //buff_.data(),       //buff_ is used to receive message. async_write works if I send it back directly  
                   // boost::asio::buffer("Hello World!"),  //Works if I directly put string in here.  
                   boost::asio::buffer(msg),   //Doesn't work using string var  
                    strand_.wrap(boost::bind(&session::http_do_read, shared_from_this(),  
                    boost::asio::placeholders::error)));   
```  
  
#   
  
When I put my message into string, it send to firefox. Firefox disconnect the connection. The error message is "The connection to wss://www.flatworld.com:8443/ was interrupted while the page was loading." When debug from Chrome, the error message is " chat.html:135 WebSocket connection to 'wss://www.flatworld.com:8443/' failed: Could not decode a text frame as UTF-8.onLoad @ chat.html:135"  
  
Can you please help?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-10-17 10:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254174424  

You're missing a strand here:  
https://github.com/goflatworld/beasttest/blob/master/session.cpp#L25  
  
Here, you are passing a buffer from a local variable:  
https://github.com/goflatworld/beasttest/blob/master/session.cpp#L67  
  
From http://vinniefalco.github.io/beast/beast/ref/websocket__stream/async_write.html  
_"The caller is responsible for ensuring that the memory locations pointed to by buffers remains valid until the completion handler is called."_  
  
When the `std::string` goes out of scope, your buffer becomes invalid. Try making the string a member of the class instead.

---

## Comment 13

> Username: goflatworld  
> Created at: 2016-10-17 11:29:42 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254182287  

Great thanks. Yes. The string was out of scope. After making it part of the class, it works. Thanks again.

---

## Comment 14

> Username: goflatworld  
> Created at: 2016-10-18 10:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254473523  

Thanks for your great help so far. I have added the websocket part to  my server (not the one on github, the server is kind a commercial software, not really comfortable to put it on github ). During test,  it crashes quite often. When the server crashes, the output is as below:  
  
#   
  
server: /usr/include/beast/websocket/detail/invokable.hpp:138: void beast::websocket::detail::invokable::emplace(F&&) [with F = beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_frame_op<beast::prepared_buffers<beast::consuming_buffers<boost::asio::const_buffers_1, boost::asio::const_buffer> >, beast::websocket::streamboost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp > >::write_op<boost::asio::const_buffers_1, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, boost::_bi::bind_t<void, boost::_mfi::mf1<void, websocket::applications::chat::session, const boost::system::error_code&>, boost::_bi::list2boost::_bi::value<std::shared_ptr<websocket::applications::chat::session >, boost::arg<1> (*)()> >, boost::asio::detail::is_continuation_if_running> > >]: Assertion `! base_' failed.  
  
#   
  
My server has two clients connect to it: one is a websocket client (I call it control), another one is normal socket client (I call it agent).   
  
I uses "control" client to control the "agent" (when I say control, I meant I type some command on the "control", it sends to server, server sends to "agent"). Then "agent" performs the required functions and sends result back.   
  
During test, if I send same command from "control" to "agent", it works  sometimes but crashes another time.  
  
I also have a normal socket client to control the "agent". It worked fine. I want to replace the normal client with websocket client.  
  
Can you please give me some hints on where should I look into it?  
  
If code is required, can I send to you via email?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2016-10-18 11:23:12 UTC  
> Url: https://github.com/boostorg/beast/issues/143#issuecomment-254479319  

Please open this as a new issue!
