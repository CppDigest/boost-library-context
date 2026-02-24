# #82 - Boost.Asio like async_read [Closed]

> Username: redboltz  
> Created at: 2016-09-21 06:09:35 UTC  
> Updated at: 2016-09-21 20:37:14 UTC  
> Closed at: 2016-09-21 20:37:14 UTC  
> Url: https://github.com/boostorg/beast/issues/82  

Hi Vinnie,  
  
I asked a question about async_read in person at CppCon 2016. Thank you for writing a great library. And you lightning talks was fantastic!  
  
I couldn't describe my question at that time due to my poor English. Let me describe my question.  
  
See the following Boost.Asio's example:  
http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/example/cpp03/chat/chat_server.cpp  
  
``` cpp  
  void start()  
  {  
    room_.join(shared_from_this());  
    boost::asio::async_read(socket_,  
        boost::asio::buffer(read_msg_.data(), chat_message::header_length),  
        boost::bind(  
          &chat_session::handle_read_header, shared_from_this(),  
          boost::asio::placeholders::error));  
  }  
```  
  
boost::asio::async_read calls the handler `chat_session::handle_read_header` when `chat_message::header_length` bytes of data are received. The caller can assume that the data size is exactly `chat_session::handle_read_header` bytes if error code is success. Even if a sender write the concatenated header and body as one call. That means the receiver can control the length of receiving data.  
  
In `chat_session::handle_read_header`, calls async_read as the same mannar. The length is `read_msg_.body_length()`.  
  
``` cpp  
  void handle_read_header(const boost::system::error_code& error)  
  {  
    if (!error && read_msg_.decode_header())  
    {  
      boost::asio::async_read(socket_,  
          boost::asio::buffer(read_msg_.body(), read_msg_.body_length()),  
          boost::bind(&chat_session::handle_read_body, shared_from_this(),  
            boost::asio::placeholders::error));  
    }  
    else  
    {  
      room_.leave(shared_from_this());  
    }  
  }  
```  
  
I'd like to do the same thing using Beast C++ websocket library's async_read. Because I have many those kinds of codes using Boost.Asio, and if Beast C++ websocket library supports the similar functionality, it is easy to write abstraction layer that supports both TCP/IP (Boost.Asio) and Websocket (Beast).   
  
It is a feature request. But if there is better solution, any advises are welcome.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-21 15:29:16 UTC  
> Url: https://github.com/boostorg/beast/issues/82#issuecomment-248648291  

Let me make sure I understand what you're trying to do. You want to read incremental websocket message data, into a caller provided **MutableBufferSequence**, instead of passing a **DynamicBuffer**? So that you can control the exact number of bytes that are transferred in each call?  
  
An early version of `websocket::stream` worked this way, but this type of interface is not friendly to the permessage-deflate extension, which allows websocket messages to be compressed. Implementing the feature you desire, would require that the Beast websocket buffer a lot of data. And there is no benefit to the backpressure you think you're getting, because of this buffering. So I made the decision to make a complete websocket message the unit of exchange rather than a specific number of bytes. In fact, I am now questioning my decision even to provide the `read_frame` function, since that complicates the implementation of permessage-deflate and it can only get callers into trouble while providing no benefit - any entity along the way can re-frame websocket messages so you can't carry data through by manipulating frame lengths. And it would be totally okay for the `beast::websocket::stream` to combine all the frames in a message into one buffer - so callers can't assume anything about the way messages are framed.  
  
I think this was the right choice, although I am certainly open to arguments for why this should be changed. I am not a WebSocket expert, if someone with more experience could chime in that would be great. For example, are messages usually small? Is there any use-case for sending a huge message (say, a gigabyte)? Or do most application layers break this up into multiple messages with sequence numbers or some such, using sub protocols?  
  
To answer your question about the chat server, I think there is value in allowing for abstraction of the transport - you want the chat program to work over both regular tcp/ip and WebSocket. But I'm not sure that the right place to do that is at the lowest level (i.e. in the physical call to `async_read`). Instead, you could design a C++ interface to an abstract chat endpoint, and then implement two concrete classes - one for regular tcp/ip and the other for WebSocket.

---

## Comment 2

> Username: redboltz  
> Created at: 2016-09-21 20:37:14 UTC  
> Url: https://github.com/boostorg/beast/issues/82#issuecomment-248735296  

@vinniefalco , thank you for the quick response.  
  
> Let me make sure I understand what you're trying to do. You want to read incremental websocket message data, into a caller provided MutableBufferSequence, instead of passing a DynamicBuffer? So that you can control the exact number of bytes that are transferred in each call?  
  
That is exactly what I wanted to do.  
  
> An early version of websocket::stream worked this way, but this type of interface is not friendly to the permessage-deflate extension, which allows websocket messages to be compressed. Implementing the feature you desire, would require that the Beast websocket buffer a lot of data.  
  
True. I agree with your design decision.  
I'm convinced.  
  
> To answer your question about the chat server, I think there is value in allowing for abstraction of the transport - you want the chat program to work over both regular tcp/ip and WebSocket. But I'm not sure that the right place to do that is at the lowest level (i.e. in the physical call to async_read). Instead, you could design a C++ interface to an abstract chat endpoint, and then implement two concrete classes - one for regular tcp/ip and the other for WebSocket.  
  
Thank you for the advice. I realized that my design, similar to the chat example, is too much depends on MutableBufferSequence and boost::asio::aync_read mechanism. There are many protocols and not all protocols allow fixed size read effectively. So I need to design adapting layer at upper layer than communication layer.
