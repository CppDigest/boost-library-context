# #1382 - can't read websocket buffer with lambda [Closed]

> Username: Ludea  
> Created at: 2018-12-20 18:55:39 UTC  
> Updated at: 2018-12-23 11:16:13 UTC  
> Closed at: 2018-12-23 11:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1382  

Hello,  
  
I try to do an asynchronous read into a websocket, using lambda. But I get `read : Operation Canceled`  
So I don't read the right socket I think.  
I can connect to my websocket server, I have no error.  
  
Here my code :   
```cpp  
  template <typename Handler>  
    void do_read(boost::beast::multi_buffer &buffer, Handler callback)  
    {  
	auto self(shared_from_this());  
	m_ws.async_read(buffer,  
                [self, buffer](boost::system::error_code ec, std::size_t /*length*/)  
                {  
                        if (!ec)  
                        {  
				 std::cout<< "message : " << boost::beast::buffers_to_string(buffer.data())<< std::endl;  
                        }  
                        else  
                 	       std::cout<<"read : " << ec.message()<<std::endl;  
                });  
    }  
```  
  
I call `do_read` like this   
```cpp  
m_websocket->do_read(buffer,  
		[buffer](boost::system::error_code ec)  
 		{  
			std::cout<<"mess : " << buffer.data()<<std::endl;  
		});  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-20 18:57:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1382#issuecomment-449100750  

Looks like the socket is beyond destroyed, which is causing the pending read operation to become canceled. You likely have lifetime issues.

---

## Comment 2

> Username: Ludea  
> Created at: 2018-12-21 05:39:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1382#issuecomment-449261018  

I start to create a websocket_session instance  
```cpp  
websocket_session_ptr new_websocket(new websocket_session(std::move(socket)));  
        new_websocket->run();  
        chatwebsocketsessionpointer session = chat_websocket_session::create(room, new_websocket);  
        room->join(session);  
        wait_for_connection();  
```  
Into `run() ` I accept the incoming connection   
```cpp  
m_ws.async_accept(  
            boost::asio::bind_executor(  
                strand,  
                boost::bind(  
                    &websocket_session::on_accept,  
                    shared_from_this(),  
                    boost::asio::placeholders::error)));  
```  
After i create a chatpointersession   
```cpp  
static chatwebsocketsessionpointer create(chatroompointer room, websocket_session_ptr websocket )  
        {  
                chatwebsocketsessionpointer session (new chat_websocket_session(room, websocket));  
                session->wait_for_data();  
                return session;  
        }  
```  
The async_read function is into `wait_for_data `,  
So I keep the same socket.   
  
I don't understand why I have lifetime issue.   
I read the socket right now the websocket connection

---

## Comment 3

> Username: Ludea  
> Created at: 2018-12-23 11:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1382#issuecomment-449629584  

I try to read the socket before I connect to it.  
I fix it
