# #2668 - question: moving tcp socket from Session class to another class [Closed]

> Username: kA31928  
> Created at: 2023-04-11 14:50:17 UTC  
> Updated at: 2023-10-28 16:33:52 UTC  
> Closed at: 2023-10-28 16:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2668  

1. After creating the websocket and establishing a connection, I would like to move the socket from the ServerSession class to a different class EventHandler  (which has multiple derived classes) that will be responsible for doing the write operations but it seems not possible.  
Below is the header files of what I want to do. The last class EventHandler wants to get a copy or reference of the socket created in the ServerSession class as this class launches different threads in response to specific events and continuously will write to client.  
```  
class Listener : public std::enable_shared_from_this<Listener>  
{  
    net::io_context &mIoc;  
    tcp::acceptor mAcceptor;  
public:   
    Listener(boost::asio::io_context &ioc, std::string ip, unsigned short int port);  
    ~Listener();  
    void onAccept(beast::error_code ec, tcp::socket socket);  
    void asyncAccept();  
private:  
    unsigned short int mPort;  
    std::string mIp;  
    int mConnectionCounter;  
};  
```  
```  
class ServerSession : public std::enable_shared_from_this<ServerSession>  
{  
    websocket::stream<beast::tcp_stream> mWebSocket;                                               ///< Web socket.   
    beast::flat_buffer mSocketBuffer;  
public:  
    void run();  
private:   
    void onRun();  
    void onAcceptConnection(beast::error_code ec);  
    void readMessage();  
    void onRead(beast::error_code ec, std::size_t bytes_transferred);      
    EventHandler *mHandler;  
};  
```  
```  
class EventHandler  
{  
    websocket::stream<beast::tcp_stream> mWebSocket;                                               ///< Web socket.   
public:   
    EventHandler();  
    ~EventHandler();  
    void setSocket(tcp::socket&& socket) {mWebSocket = socket};  
    void write();  
   
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-04-11 14:54:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2668#issuecomment-1503526273  

Please format you code correctly.  
  
Generally for asio: moving io objects is legal as long as there's not operation going on (which makes it UB)

---

## Comment 2

> Username: kA31928  
> Created at: 2023-04-11 15:16:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2668#issuecomment-1503587230  

how would I move the stream or tcp object though ?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-05-15 02:01:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2668#issuecomment-1547089321  

With `std::move` ?

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-18 17:26:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2668#issuecomment-1684219052  

@kA31928 Is this still open?
