# #2740 - Weird Error with websocket.write [Closed]

> Username: Jenovesan  
> Created at: 2023-09-18 18:24:43 UTC  
> Updated at: 2023-09-19 17:08:42 UTC  
> Closed at: 2023-09-19 17:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2740  

Beast Version: 347  
  
I'm trying to make a simplified websocket class for WSS connections but I'm getting this weird error.  
In my constructor, I have:  
```  
Websocket::Websocket(const std::string& host, const std::string& target, const std::string& service)  
{  
    // Setup SSL context  
    boost::asio::io_context io_context;  
    ssl::context ctx{ssl::context::sslv23_client};  
  
    // Set the certificate verification paths.  
    ctx.set_default_verify_paths();  
  
    // Establish SSL connection  
    ssl::stream<tcp::socket> socket{io_context, ctx};  
    tcp::resolver resolver{io_context};  
    auto const results = resolver.resolve(host, service);  
    boost::asio::connect(socket.next_layer(), results.begin(), results.end());  
    socket.handshake(ssl::stream_base::handshake_type::client);  
  
    // WebSocket handshake  
    ws = new websocket::stream<ssl::stream<tcp::socket>>(std::move(socket));  
    ws->handshake(host, target);  
}  
```  
  
Then I have this method:  
```  
void Websocket::send(nlohmann::json msg)  
{  
    ws->write(boost::asio::buffer(msg.dump()));  
}  
```  
  
Then in my main.cpp I run:  
`Websocket ws = Websocket("***", "***", "443");`  
With this, the WebSocket connects successfully, performing the handshake. (with an actual URL)  
  
and then I go to authenticate with   
```  
nlohmann::json authentication_message = {  
        {"action", "auth"},  
        {"params", "***"}  
    };  
ws.send(authentication_message)  
```  
But I get a System Error with the error code 10009 when it calls `ws->write`.  
  
But the thing that I can't explain though is if I move the authentication code from my main.cpp to the constructor after the handshake, it all works perfectly with no errors.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-19 00:20:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2740#issuecomment-1724654598  

You're constructing the `io_context` and the `ssl::context` on the stack in the constructor, and passing them by reference to the socket. They'll be destroyed when you exit the constructor so you'll have dangling reference.  
  
Are you coming from a reference counted language? If so, you'll need to start thinking about lifetimes of objects. References don't keep things alive in C++.

---

## Comment 2

> Username: Jenovesan  
> Created at: 2023-09-19 17:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2740#issuecomment-1726105848  

That worked, thank you for helping me with that.   
I suppose I didn't consider that the websocket uses the io context and SSL context throughout the websocket's lifespan (perhaps it's because I am not very familiar with networking).  
I am coming from a reference-counted language but have been coding in C++ for a few months now so I'm surprised this is my first experience where I've had to consider what I'm passing into an object that's stored as a pointer.  
I apologize for creating this issue when the issue was just due to my inexperience. Thank you though regardless.
