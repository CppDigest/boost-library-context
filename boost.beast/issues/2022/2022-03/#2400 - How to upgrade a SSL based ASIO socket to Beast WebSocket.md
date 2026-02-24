# #2400 - How to upgrade a SSL based ASIO socket to Beast WebSocket? [Closed]

> Username: wonderbee77  
> Created at: 2022-03-26 04:43:41 UTC  
> Updated at: 2022-09-24 05:08:34 UTC  
> Closed at: 2022-09-24 05:08:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2400  

Can you provide me an example code where the async SSL-based ASIO socket is moved to the beast socket to open up the websocket?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-26 05:32:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079610628  

Sure, this is possible with latest asio as asio ssl streams became moveabke in boost 1.68

---

## Comment 2

> Username: wonderbee77  
> Created at: 2022-03-26 15:05:48 UTC  
> Updated at: 2022-03-26 15:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079711981  

Any example code? I use boost 1.75. damn sure this is possible.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-03-26 15:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079712331  

I can knock something up when I get home

---

## Comment 4

> Username: wonderbee77  
> Created at: 2022-03-26 15:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079712411  

Thank you so much:)

---

## Comment 5

> Username: wonderbee77  
> Created at: 2022-03-26 21:28:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079777934  

One thing I want to mention, I did the handshake using the Boost ASIO library and then moved the socket to Beast. After moving, I just have code to accept() and read() asynchronously.

---

## Comment 6

> Username: wonderbee77  
> Created at: 2022-03-26 21:34:42 UTC  
> Updated at: 2022-03-26 21:36:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079778865  

seems like I am getting an uncaught exceptions when moving the socket:  
  
`class webSocketConnection {  
        public:  
            using con_handle_t = std::list<std::shared_ptr<Connection>>::iterator;  
            boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> web_socket;  
              
            webSocketConnection(    con_handle_t con_handle,   
                                    boost::asio::ssl::context& ssl_context ) :  
                                web_socket(std::move(con_handle->get()->ssl_socket.next_layer()), ssl_context) { web_socket.accept(); }  
    };`  
  
Creating object:  
`                capegen::webSocketConnection webSocketConnectionObj(con_handle, ssl_context);  
`

---

## Comment 7

> Username: wonderbee77  
> Created at: 2022-03-26 22:06:10 UTC  
> Updated at: 2022-03-26 22:06:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079783745  

seems like the boost ASIO handshake is not working if we open up a web socket connection from the browser...

---

## Comment 8

> Username: wonderbee77  
> Created at: 2022-03-26 22:10:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079784356  

OK. I am confused. I will just wait for your example program.

---

## Comment 9

> Username: wonderbee77  
> Created at: 2022-03-26 22:24:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1079786118  

boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> web_socket;  
  
Is the above code correct to define a web socket?  
  
How do we move the SSL request and aync_accept on the Beast side?  
  
Drilling down, I believe I face trouble in the moving request, accepting WebSocket connection.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-06-14 17:36:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2400#issuecomment-1155500473  

Is this resolved?
