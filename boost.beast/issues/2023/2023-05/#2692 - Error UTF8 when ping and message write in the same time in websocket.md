# #2692 - Error UTF8 when ping and message write in the same time in websocket [Closed]

> Username: DenyShew  
> Created at: 2023-05-31 13:54:33 UTC  
> Updated at: 2023-12-31 10:40:56 UTC  
> Closed at: 2023-12-31 10:40:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2692  

Hello, I am using boost version 1.81.  
  
I have an error in websocket server, when it sends ping and some message to client in the same time.  
I captured it using wireshark, you can see that message is corrupted after sending ping in automatic mode.  
  
After I turned automatic ping off and started send it exactly between messages it started working.  
Setting options for server:  
`ws_.set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::server));`  
  
Where can be the problem?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-05-31 14:17:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2692#issuecomment-1570327677  

When it sends? Are you initializing multiple async ops at once

---

## Comment 2

> Username: DenyShew  
> Created at: 2023-05-31 14:33:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2692#issuecomment-1570356721  

Yes, I am initializing async_read after reading and async_write when some event happens

---

## Comment 3

> Username: DenyShew  
> Created at: 2023-06-06 08:54:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2692#issuecomment-1578230343  

An example of code that imitates an error  
  
[main.txt](https://github.com/boostorg/beast/files/11662356/main.txt)

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-18 07:45:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2692#issuecomment-1683504757  

This looks like you might be initializing another write before the first completed:  
  
```cpp  
    void  
    on_timer(boost::system::error_code ec)  
    {  
        if(ec.failed())  
        {  
            return fail(ec, "timer");  
        }  
          
        if(ws_.is_open())  
        {  
            std::string message = "Message";  
            buffer_.consume(buffer_.size());  
            ws_.text(true);  
            ws_.async_write(  
            std::move(boost::asio::buffer(message)),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
        }  
  
        timer.expires_after(std::chrono::milliseconds(30));  
        timer.async_wait(boost::bind(&session::on_timer, shared_from_this(), boost::placeholders::_1));  
    }  
```
