# #1376 - Boost::shared_ptr vs std::make_shared [Closed]

> Username: Ludea  
> Created at: 2018-12-18 17:05:30 UTC  
> Updated at: 2018-12-29 13:08:02 UTC  
> Closed at: 2018-12-29 13:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1376  

Hello,  
  
I try to use websocket_session with boost::shared_ptr.   
I create a variable of type websocket_session, instantiate it to do accept connection, but I get `bad file descriptor `  
So I guess I try to open the wrong socket.   
  
Here my code   
  
Server  
```cpp  
void tcp_server::wait_for_connection()  
{  
        acceptor.async_accept(  
            socket,  
            boost::bind(  
                &tcp_server::handle_accept,  
                this,  
                boost::asio::placeholders::error  
                ));  
}  
  
void tcp_server::handle_accept(const boost::system::error_code ec)  
{  
        if (!ec)  
        {  
        websocket_session_ptr new_websocket(new websocket_session(std::move(socket))); //instantiation   
        new_websocket->run();  
        wait_for_connection();  
        }  
}  
```  
  
WebSocket session.cpp  
```cpp  
void websocket_session::run()  
{                                                                                    // Accept the websocket handshake  
        std::cout <<"accepted"<< std::endl;  
        m_ws.async_accept(  
            boost::asio::bind_executor(  
                strand,  
                std::bind(&websocket_session::on_accept,  
                    this,  
                    std::placeholders::_1)));  
}  
  
void websocket_session::on_accept(boost::system::error_code ec)  
{  
        if(ec)  
            return fail(ec, "accept");  
}   
```  
  
WebSocket session.h  
```cpp  
typedef boost::shared_ptr<websocket_session> websocket_session_ptr;   
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-18 17:20:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-448299825  

I don't see any calls to `shared_from_this`

---

## Comment 2

> Username: Ludea  
> Created at: 2018-12-18 17:49:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-448309289  

To call `shared_from_this` the class must inherit to enable shared_from_this`?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-18 18:28:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-448321479  

https://www.boost.org/doc/libs/1_69_0/libs/smart_ptr/doc/html/smart_ptr.html#enable_shared_from_this

---

## Comment 4

> Username: Ludea  
> Created at: 2018-12-18 20:00:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-448350547  

Ok, I inherit to `enable shared_from_this`, and replace this, to `shared_from_this `, and I can connect to the socket.  
Thanks for help!

---

## Comment 5

> Username: Ludea  
> Created at: 2018-12-24 13:46:21 UTC  
> Updated at: 2018-12-24 13:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449736259  

If I move `websocket_session::run` into listener class, it is not possible to do  
```cpp  
new_websocket->m_ws.async_accept(  
boost::asio::bind_executor(strand,                                    boost::bind(  
                    &tcp_server::on_accept,  
                    shared_from_this(),  
                    boost::asio::placeholders::error,                     new_websocket  
                    )));  
```  
`shared_from_this ` is not declared.  
If I use `this ` I have `bad weak ptr `

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-12-24 13:51:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449736727  

Does the unmodified example program work for you?

---

## Comment 7

> Username: Ludea  
> Created at: 2018-12-24 13:54:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449737027  

Yes, the example works

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-12-24 14:12:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449738829  

Well, whatever you changed, must be wrong.

---

## Comment 9

> Username: Ludea  
> Created at: 2018-12-24 14:50:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449742633  

The only thing, it's the server is not a shared_ptr, like : https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/example/cpp11/chat/chat_server.cpp

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-12-24 14:56:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449743259  

I don't understand

---

## Comment 11

> Username: Ludea  
> Created at: 2018-12-24 15:05:33 UTC  
> Updated at: 2018-12-24 15:05:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449744118  

The server does not inherit from `enable_shared_from_this `  
```cpp  
class tcp_server  
{                                                      
    tcp::acceptor acceptor;  
    tcp::socket socket;  
    chatroompointer     room;  
...   
}  
```  
In beast example, the  listener inherit from `enable_shared_from_this ` whereas asio example not, like my server

---

## Comment 12

> Username: Ludea  
> Created at: 2018-12-26 17:13:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-449995679  

I think I don't get the right shared_ptr.  
```cpp  
new_websocket->connected.connect(  
                [this, new_websocket]()  
                {  
                boost::asio::deadline_timer timer(new_websocket->m_ws.next_layer().get_executor().context());  
                timer.expires_from_now(boost::posix_time::seconds(5));  
                timer.wait();  
                        std::cout<< "signal ! "<< std::endl;  
                        chatwebsocketsessionpointer session = chat_websocket_session::create(room, new_websocket);  
                });  
  ```  
I add a signal into websocket_session to get the socket accepted.   
I add a timer to wait in case the acceptor take time to accept it.   
I captur this signal and create a new shared ptr `chat_websocket_session ` with websocket_session as arguments and have still issue

---

## Comment 13

> Username: Ludea  
> Created at: 2018-12-29 13:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1376#issuecomment-450492107  

I restart from scratch, my issue is fix
