# #1324 - Still in CONNECTING state [Closed]

> Username: Ludea  
> Created at: 2018-11-26 19:01:39 UTC  
> Updated at: 2018-11-27 10:13:28 UTC  
> Closed at: 2018-11-27 10:13:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1324  

Hello,   
  
I use the websocket server example : https://www.boost.org/doc/libs/1_68_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp  
  
For clients side I use a browser, so I create a javascript app with https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API  
  
I can connect to the server, but when I try to send a message, I have the message : `Still in CONNECTING state`.  
So I can not read the websocket message.  
I use boost 1.68.  
Any idea what is wrong?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-26 19:23:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441764054  

No idea, it works fine for me. I am using Chrome. Are you using plain, or are you using SSL?

---

## Comment 2

> Username: Ludea  
> Created at: 2018-11-26 19:31:23 UTC  
> Updated at: 2018-11-26 19:31:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441767017  

I use Chrome with plain.  
Here my js code : ```socket = new WebSocket('ws://localhost:4000');  
socket.onopen = () =>  
socket.send("here client");```  
  
I modified the websocket server, to print the message :  
```  
void websocket_session::on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
           fail(ec, "read");  
       
        ws.text(ws.got_text());  
        std::cout << "message : " << boost::beast::buffers(buffer.data()) <<  std::endl;  
        do_read();  
}```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-26 19:34:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441768478  

When you launch the server, are you launching it on port 4000?

---

## Comment 4

> Username: Ludea  
> Created at: 2018-11-26 19:43:28 UTC  
> Updated at: 2018-11-26 19:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441771989  

```int main()  
{                                                                                                   
        try  
        {                                                                                          boost::asio::io_context ioc;  
                tcp::endpoint endpoint(tcp::v4(), 4000);  
                std::shared_ptr<std::string const> const&doc_root = std::make_shared<std::string>("/");  
                tcp_server server(ioc, endpoint, doc_root);  
                ioc.run();  
        }  
        catch (std::exception& e)  
        {  
                std::cerr << e.what() << std::endl;  
        }  
  
        return 0;  
}  
```  
  
I can see the accepted message   
  
```void websocket_session::run()  
{  
        // Accept the websocket handshake  
        std::cout <<"Accepted! "<< std::endl;  
        ws.async_accept(  
            boost::asio::bind_executor(  
                strand,  
                std::bind(  
                    &websocket_session::on_accept,                                                                    shared_from_this(),  
                    std::placeholders::_1)));  
}  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-11-26 19:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441776134  

It works on my end, and no one else has reported this. So the problem must be unique to your configuration. You will need to debug it to figure out why it isn't working.

---

## Comment 6

> Username: Ludea  
> Created at: 2018-11-26 20:51:50 UTC  
> Updated at: 2018-11-26 21:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441793551  

Sometimes, `message` is screen into a loop, with empty variable.   
It happens with a websocket tester application (not browser)  
And app logs: timeout error

---

## Comment 7

> Username: Ludea  
> Created at: 2018-11-26 21:14:00 UTC  
> Updated at: 2018-11-26 21:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-441800084  

Ok, I found where is the issue :  
`accept: The WebSocket stream was gracefully closed at both endpoints` in on_accept() function whereas I never close the connection

---

## Comment 8

> Username: Ludea  
> Created at: 2018-11-27 10:13:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1324#issuecomment-442002922  

I fix the issue.  
It was a client side issue
