# #2530 - [question] some doubts while using async_write [Closed]

> Username: ILikeIneine  
> Created at: 2022-10-08 10:02:07 UTC  
> Updated at: 2023-08-25 07:43:06 UTC  
> Closed at: 2023-08-25 07:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2530  

### Version of Beast  
  
1.74  
  
### Description  
  
I'm trying to write a message client like [chatterbox](https://github.com/test-scenarios/boost_beast_websocket_echo/blob/1d17ebe265381b5967f0a8139ced631c402afedf/pre-cxx20/chatterbox/connection.cpp)  
  
But the situation in my project is a little different. I separate it as two parts. One part for sending-reading and handle the websocket connection, another for message processing part that process the message we received and generate some message that need to be send. It is a typical `producer-consumer` model.   
  
The connection part just need to take message from handler's queue when there is a message to be consume, and meanwhile. receive message from remote and add to handler's queue. I use asio here and basically all the same as the chatterbox above until the `handle_handshake`.   
  
here is part of my code:   
```  
// as async_handshake's HandshakeHandler  
void  
connection_impl::init_session()  
{  
    LOG_INFO("[info] On HeartBeating ...");  
      
    // async_read  
    initiate_rx();  
    may_be_send_next();  
  
    //if (!writing_thread_.joinable()) {  
    //    writing_thread_ = std::thread( [self=shared_from_this()] {  
    //        self->sync_write();  
    //    });  
    //}  
  
    //if (!read_thread_.joinable()) {  
    //    read_thread_ = std::thread( [self = shared_from_this()] {  
    //        self->sync_read();  
    //    });  
    //}  
  
}  
  
```  
```  
void  
connection_impl::may_be_send_next()  
{  
    const auto sh = sh_.lock();  
  
    while (!sh->IsExit())  
    {  
        if (sending_state_ == sending)  
            continue;  
        LOG_INFO("[info] wait generate message");  
  
        // check and get a message from queue  
        // this might block with a timeout,  using conditional_variable's wait_for msg producing  
        const std::string msg_str = sh->SeizeOneSendMessageAsString();    
        send(msg_str);  
    }  
}  
  
void  
connection_impl::send(std::string msg)  
{  
    // deprecate empty message  
    if (msg.empty())   
        return;  
  
    net::dispatch(  
        net::bind_executor(ws_->get_executor(), [self = shared_from_this(), message = std::move(msg)]  
        {  
            self->initiate_tx(message);  
        }));  
}  
  
void  
connection_impl::initiate_tx(const std::string& msg)  
{  
    assert(sending_state_ == send_idle);  
      
    sending_state_ = sending;  
    ws_->async_write(net::buffer(msg),  
        [self = shared_from_this()](const beast::error_code& ec, size_t bytes_transferred)  
    {  
        LOG_INFO("[info]: write transferred bytes: {}", bytes_transferred);  
        self->handle_tx(ec);  
    });  
}  
  
void  
connection_impl::handle_tx(error_code ec)  
{  
    assert(sending_state_ == sending);  
  
    if (ec) {  
        LOG_ERROR("[error]: write, {}", ec.message());  
        return;  
    }  
    sending_state_ = send_idle;  
}  
  
```  
  
`async_read` performs well. But in this way I can only receive msg and the message can't be send out by `async_write`. Seems async_write is blocked in io_context executor. I guess the reason was the while-loop in `may_be_send_next()`?   
  
I wonder what's the proper way to solve this situation using async actions when the messages need to send are uncertain( may block to wait message generation ) It seems rather ambivalent...  
  
or should I start two thread at `init_session()` for sync reading and sync writing like the code I commented?  
  
here is the code of my issue [link](https://github.com/ILikeIneine/boost-asio-issue)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-10 07:06:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1272865926  

How do you run the `io_context` ?

---

## Comment 2

> Username: ILikeIneine  
> Created at: 2022-10-10 07:42:29 UTC  
> Updated at: 2022-10-10 07:42:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1272904220  

> How do you run the io_context ?  
  
I run it in a new thread, basically like this  
  
```  
Net_Thread_ = std::thread([self = shared_from_this()] {  
        self->connection_impl_->run();  
        self->ioc_.run();  
    });  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-11 16:29:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1274965515  

what does `connection_impl->run()` do? And are you using the async_* function from outside the `Net_Thread` without dispatching?

---

## Comment 4

> Username: ILikeIneine  
> Created at: 2022-10-12 03:17:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1275528519  

> are you using the async_* function from outside the Net_Thread without dispatching?  
  
No, I didn't.   
  
I have a class `service_handler` that contains `io_context` and `connection_impl_`. The `connection _impl` is constructed with `io_context&`.  
  
when I call `start` on service_handler, it will start multiple working thread. One of them is `Net_Thread`, which is the code above.  
  
what   
  
> self->connection_impl_->run();  
  
does is like:  
(sorry for long codes pasted here😣, but you can check the code in [this github repository](https://github.com/ILikeIneine/boost-asio-issue))  
```  
void  
connection_impl::run()  
{  
    net::dispatch(ws_->get_executor(), [self = shared_from_this()]  
        {  
            self->handle_run();  
        });  
}  
```  
```  
void  
connection_impl::handle_run()  
{  
    ws_->binary(true);  
  
    const auto remote_ep = net::ip::tcp::endpoint{  
        net::ip::make_address(remote_info_.host_), remote_info_.port_ };  
  
    beast::get_lowest_layer(*ws_).async_connect(remote_ep,  
        net::bind_executor(ws_->get_executor(), [self = shared_from_this()](error_code ec)  
    {  
        if (ec)   
        {  
            LOG_ERROR("connect error: {}", ec.message());  
            return;  
        }  
        self->handle_connect(ec);  
    }));  
  
}  
```  
  
```  
void  
connection_impl::handle_connect(error_code ec)  
{  
     if (ec)  
    {  
        LOG_ERROR("[error] connect, {0}", ec.message());  
        //self->try_reconnect();  
        return;  
    }  
    initiate_handshake();  
}  
```  
  
```  
void  
connection_impl::initiate_handshake()  
{  
    // still tcp stream  
    //beast::get_lowest_layer(*ws_).expires_after(30s);  
  
    LOG_INFO("\n[info] Session Connection Established...");  
    const auto sh = sh_.lock();  
  
    ws_->set_option(websocket::stream_base::timeout::suggested(beast::role_type::client));  
    ws_->set_option(websocket::stream_base::decorator(  
        [](websocket::request_type& req)->void  
        {  
            req.set(http::field::user_agent, std::string(BOOST_BEAST_VERSION_STRING) +  
                "BoleanGuard Client Websocket");  
        }));  
    init_tcp_handshake();  
  
}  
  
void  
connection_impl::init_tcp_handshake()  
{  
    LOG_INFO("[info] SSL handshake successfully finished!");  
  
    const auto remote_host = remote_info_.host_ + std::to_string(remote_info_.port_);  
    ws_->async_handshake(remote_host, remote_info_.subUrl_,  
        [self = shared_from_this()](beast::error_code ec)  
    {  
        self->handle_handshake(ec);  
    });  
  
}  
```  
  
```  
void  
connection_impl::handle_handshake(error_code ec)  
{  
    if (ec)  
    {  
        //upgrade_declined  
        LOG_ERROR("[error]: websocket handshake fail, code{}, {}", ec.value(), ec.message());  
        return;  
    }  
    LOG_INFO("[info] Handshake Finished...");  
  
    // change corresponding status  
  
    state_ = chatting;  
    init_session();  
}  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-10-12 08:26:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1275783276  

hwo do you call `connection_impl::run()` and where to you call `io_context::run()` ?

---

## Comment 6

> Username: ILikeIneine  
> Created at: 2022-10-12 09:14:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1275848425  

1. first I construct `service_handler`, `connection_impl` is a member of `service_handler`  
2. in main thread I call `service_handler.run()`  
3. in `service_handler.run()`, I write the code below  
  
```  
// Net_Thread is part of service_handler  
Net_Thread_ = std::thread([self = shared_from_this()] {  
  
        // run() was the code I pasted  
        self->connection_impl_->run();  
  
        // here is the _io_context_, I call it and only call it here  
        self->ioc_.run();  
    });  
```

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2022-10-12 15:43:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1276387730  

Ok, so you only initiate async_wrtie from the Net_Thread_?   
  
Can you please alborate on this statement:  
  
> I wonder what's the proper way to solve this situation using async actions when the messages need to send are uncertain ( may block to wait message generation ) It seems rather ambivalent...

---

## Comment 8

> Username: ILikeIneine  
> Created at: 2022-10-13 09:43:58 UTC  
> Updated at: 2022-10-13 09:44:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1277339172  

what I want is:  
```  
// call in async_handshake  
void handle_handshake(error_code ec)  
{  
     on_async_read();  
     on_async_write();  
}  
```  
call async_read and async_write continuously  
```  
void on_async_read()  
{  
    async_read();  
  
    // add msg to queue  
  
    ...  
}  
```   
```  
// I don't know if this is available, but it is what I expect  
void on_async_write()  
{  
    while(true)  
    {  
        // avoid call async _write twice  
        if( state = send) continue;   
  
        // get a message from queue.   
        // when there is no producer it may be blocked for a short time   
        // and will return when it is time out  
        // which means the message to send is uncertain and need to wait for other to generate... sorry for my poor description  
        auto msg = get_msg();     
  
        state = sending  
        async_write(msg,[this](error_code ec)  
        {  
             this->state = idle;  
        });  
    }  
}  
```  
It seems the while-loop calling `async_write` all failed and all messages are not sended.  
  
I wonder if it possible to use async_write to perform such behavior.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2022-10-13 12:37:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1277542841  

No that's UB. YOu need to wait for async_write to complete before initiating the next.

---

## Comment 10

> Username: ILikeIneine  
> Created at: 2022-10-14 01:53:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1278369303  

So even if I used a `status` variable passed to `async_write` to ensure there is only one async_write will exist is still ub either?

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2022-10-14 06:56:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1278567847  

I don't understand the question - `async_write` doesn't support this. You must only call `async_write` a second time once the first operation completed.

---

## Comment 12

> Username: ILikeIneine  
> Created at: 2022-10-14 08:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1278683927  

I mean using a `status` variable in while-loop. I'll check this status if it is set to **sending**.   
  
And this status change will change back to **idle** in `WriteHandler` of `async_write`.   
  
when status is **handle**, I can call async_write and meantime change the status to **sending**  
  
If the status is **sending**, I'll continue the loop, not to call async_write untill the status get changed by WriteHandler, which means this operation is completed.

---

## Comment 13

> Username: klemens-morgenstern  
> Created at: 2022-10-15 08:36:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1279695899  

No you can't, because you'll be blocking the event loop.

---

## Comment 14

> Username: dufferzafar  
> Created at: 2023-07-12 16:43:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1632874551  

@klemens-morgenstern Suppose I have a queue of messages that I need to write. Currently, I drain the queue msg by msg, so I `async_write` with `net::buffer(queue.front())`, let it complete and only then issue another write.  
  
Since I can't schedule multiple async_write operations in parallel, is it possible to issue a LARGE write? As in, I create a `net::buffer` with the entire contents of the queue and issue a single async_write over that?   
  
Will that be faster than the current approach?   
  
I have a server publishing data every second to clients, and I'm seeing increasing queue size associated with my client connections, while the boost worker threads sit idle.

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2023-07-13 00:59:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1633386074  

You can make it a buffer sequence, yes. Check the requirements in the asio docs. Or just use beast multi_buffer.  
  
I cannot tell you if it is faster.

---

## Comment 16

> Username: dufferzafar  
> Created at: 2023-07-13 04:40:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1633534417  

I found that issue later. But I think what I want is a "gather interface" for async_write #2033   
  
Like asio::async_write provides for TCP stream.  
  
How will using a buffersequence / multi_buffer help if websocket async_write doesn't support writing multiple items in one call?

---

## Comment 17

> Username: ashtum  
> Created at: 2023-08-18 07:57:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2530#issuecomment-1683518337  

@ILikeIneine Could you please close the issue if your problem has been resolved?  
@dufferzafar Discussion is continued in #2033
