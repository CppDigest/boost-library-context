# #1371 - get socket from websocket with pointer [Closed]

> Username: Ludea  
> Created at: 2018-12-14 16:14:24 UTC  
> Updated at: 2018-12-15 09:49:21 UTC  
> Closed at: 2018-12-15 09:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1371  

Hello,  
  
I try to create a websocket server with pointer.  
In listener class, I need a socket with `boost::asio::async_accept()`.  
But I don't know how get this from websocket.  
  
websocket_session.cpp  
```cpp  
websocket_session::websocket_session(tcp::socket socket)  
        : m_ws(std::move(socket))  
        , strand(socket.get_executor())  
{  
run();  
}  
  
websocket::stream<tcp::socket>& websock()  
{  
	return m_ws;  
}  
  
void websocket_session::run()  
{  
        // Accept the websocket handshake  
	std::cout <<"accepted ! "<< std::endl;  
        m_ws.async_accept(  
            boost::asio::bind_executor(  
                strand,  
                std::bind(  
                    &websocket_session::on_accept,  
		    shared_from_this(),  
                    std::placeholders::_1)));  
}  
  
````  
server.cpp  
```cpp  
websocket_session_ptr new_websocket(new websocket_session(std::move(socket)));  
	acceptor.async_accept(  
            new_websocket->m_ws,                    <- Here I need to get socket not websocket  
            boost::bind(  
                &tcp_server::handle_accept,  
                this,  
                boost::asio::placeholders::error,  
		new_websocket  
		));  
  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-14 16:37:05 UTC  
> Updated at: 2018-12-14 16:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447379831  

`new_websocket->m_ws.next_layer()`  
  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/creating_streams.html#beast.using_websocket.creating_streams.non_owning_references

---

## Comment 2

> Username: Ludea  
> Created at: 2018-12-14 16:59:44 UTC  
> Updated at: 2018-12-14 17:00:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447386551  

ok thanks ! I did not see this into the docs.  
But I can't return m_ws  
  
```cpp  
websocket::stream<tcp::socket>& websock()  
{  
	return m_ws;  
}  
```  
  
I have undeclared identifier 'm_ws'.  
So, I have to declared m_ws as public ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-14 17:19:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447392073  

This sounds like a C++ question rather than a Beast question? `m_ws` looks like a member of your class (I'm assuming).

---

## Comment 4

> Username: Ludea  
> Created at: 2018-12-14 17:26:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447394115  

Yes it is a c++ question, but it is using Beast  
```cpp  
private:  
    websocket::stream<tcp::socket> m_ws;  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-14 17:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447394694  

Well if you want to access the next layer from outside the class, then you have to provide a way for callers to get to it. Either make `m_ws` public, or add a member function which returns the underlying socket.

---

## Comment 6

> Username: Ludea  
> Created at: 2018-12-15 09:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1371#issuecomment-447555420  

I choose to make `ms_w` public, so I fix my issue
