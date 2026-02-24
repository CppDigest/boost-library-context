# #2907 - How to set the max size of WebSocket server response messages（client prompts a 1009 error message） [Closed]

> Username: star-0o0  
> Created at: 2024-07-23 01:26:38 UTC  
> Updated at: 2024-07-23 06:42:12 UTC  
> Closed at: 2024-07-23 06:42:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2907  

I developed a WebSocket service using Beast. When this service returns data that is too long (greater than 1M), it prompts to cancel the operation and disconnect the connection. At the same time, the client prompts a 1009 error message (message is too big).  
  
I want to ask how to set the maximum size of WebSocket service response message  
  
version : boost-1.67.0  
  
  
`  
  
    void on_write(boost::system::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec) {  
            LOG_ERR("write failed: %s", ec.message().c_str());  
		}  
  
        m_queue.erase(m_queue.begin());  
        if (!m_queue.empty()) {  
            m_ws.text(m_ws.got_text());  
            m_ws.async_write(boost::asio::buffer(*m_queue.front()), boost::asio::bind_executor(m_strand, std::bind(&Session::on_write,  
                shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
        }  
    }  
`  
  
![image](https://github.com/user-attachments/assets/63f36cf4-5efc-4995-bea8-4649c10c06ec)  
  
![image](https://github.com/user-attachments/assets/a7119ebb-e273-47de-a8f8-a596a77c90fe)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-23 05:35:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2907#issuecomment-2244291105  

You can use [websocket::stream::read_message_max(std::size_t amount)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_message_max/overload1.html) to override the default setting.

---

## Comment 2

> Username: star-0o0  
> Created at: 2024-07-23 05:58:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2907#issuecomment-2244315468  

> You can use [websocket::stream::read_message_max(std::size_t amount)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/read_message_max/overload1.html) to override the default setting.  
  
  
This setting has no effect, the server will still disconnect and reconnect , and the client will still prompt a 1009 error.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-07-23 06:21:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2907#issuecomment-2244344395  

Did you set the proper max_size setting on the client side as well?

---

## Comment 4

> Username: star-0o0  
> Created at: 2024-07-23 06:42:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2907#issuecomment-2244372448  

> Did you set the proper max_size setting on the client side as well?  
  
Sorry, the client did not modify the maximum message size.  
  
The problem has been resolved, and now after modifying the client settings, it works normally.
