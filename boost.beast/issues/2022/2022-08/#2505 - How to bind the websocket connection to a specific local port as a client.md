# #2505 - How to bind the websocket connection to a specific local port as a client. [Closed]

> Username: ILikeIneine  
> Created at: 2022-08-18 08:26:23 UTC  
> Updated at: 2022-08-23 08:36:26 UTC  
> Closed at: 2022-08-23 08:36:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2505  

### Version of Beast  
1.76  
  
  
### Desc  
the *Original Problem* is:   
I'm trying to emulate multiple websocket clients that work on the different machine, which means they have different IP. And the server takes the **ip as the client identifier**.(this is what they designed and I can't change it).   
  
I need to emulate this work scene, I have to create multiple websockets connection at one computer(cause I only have one). And these websocket must satisfy the requirement --- **have different IP.** I have no idea on how to solve it;  
  
the *Derived Problem* is:  
After some searching on google, I find a way to do it. It seems that I can use `iptables`  with nat to help me do the job;  
  
`sudo iptables -t nat -A POSTROUTING -d 10.30.2.xxx -p tcp --sport 6666 -j SNAT --to-source 10.30.1.xxx `  
  
using it I can redirect the specific port to another usable ip, which make a mapping. So the *Original Problem* becomes how to bind the websocket connection to a specific local port so that I can use iptables to mapping it to another ip.  
  
I find at StackOverflow, suggesting me  perform the action `bind` before `connection`.  
basically what I've done was  
```c++  
    beast::error_code err;  
    boost::asio::ip::tcp::socket sock_;  
    std::unique_ptr<websocket::stream<beast::tcp_stream>> ws_;  
  
    boost::asio::ip::tcp::endpoint local_ep(boost::asio::ip::address::from_string("127.0.0.1"), 7788);  
    sock_.open(boost::asio::ip::tcp::v4(), err);  
    if(err) { fmt::print("{}", err.message()); }  
    sock_.bind(local_ep, err);  
    if(err) { fmt::print("{}", err.message()); }  
  
    ws_ = std::make_unique<websocket::stream<beast::tcp_stream>> (std::move(sock_));  
    ws_->binary(true);  
  
   beast::get_lowest_layer(*ws_).async_connect ...  
   // ws_->async_handshake  
   // ... omit  
```  
But after done these, I still got random port, seems `bind` not working. But I've try on python as raw socket, it is workable by iptables.  
  
**What I've set:**  
![DZJ9_K@D$_Z3@3KGUQZ)BU0](https://user-images.githubusercontent.com/37239608/185345108-19fbcb75-e3c6-4a4a-bd07-3a4a1a62ab83.png)  
  
**What I've got:**  
![@@E9I7`O IQT1IA@R QQ}VJ](https://user-images.githubusercontent.com/37239608/185345323-d41f841a-4551-4765-8b84-21ab4dea0357.png)  
  
What should I suppose to do to get the correct, expected result on boost asio. To avoid mistakes that may cause the XY problem, I post the original problem as well.  
Could you show me a workable demo or something to help me achieving it? I'd really appreciate it for your working!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-18 14:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1219576402  

Your code looks correct. I think you are printing the wrong thing. `bind` configures the local endpoint, not the remote. In your code above, try printing the local endpoint of the socket after you bind it:  
```  
    sock_.bind(local_ep, err);  
    if(err) { fmt::print("{}", err.message()); }  
    fmt::print( "local endpoint: {}", sock_.local_endpoint() );  
```

---

## Comment 2

> Username: ILikeIneine  
> Created at: 2022-08-18 16:05:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1219672889  

The log I printed was from the **server side**.   
Once a connection comes in,  I print both the `local_endpoint` and the `remote endpoint` at my the server after accepted. In this case, the `remote host` should be the client's socket local_endpoint, am I right?  
  
I wonder is it the correct to perform `bind` operation before the websocket client call `async_connect` if I want to specify local endpoint to connect the remote server?   
  
The  `bind` configures the local endpoint  in my client ,  its port number still changed after `bind`, need I set REUSEADDR or REUSEPORT or something else?

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-08-18 16:16:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1219684919  

If you're on Linux, you can add new IP addresses to your loopback interface as shown here: https://askubuntu.com/a/444128  
  
then for each client, bind its socket to ("127.0.0.2", "0"), ("127.0.0.3","0") etc before connecting to the server.  
On the server side, the accepted socket's remote_endpoint will then identify which hostname is associated with the client's socket.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-18 18:22:01 UTC  
> Updated at: 2022-08-18 18:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1219798055  

Ah... I see. In your code you have:  
```  
beast::get_lowest_layer(*ws_).async_connect ...  
```  
  
The devil is in the details. What are you passing to `async_connect`? If you are passing an endpoint, then the `bind` will be preserved and it should work, because it goes through `socket::async_connect` which does not close the socket first:  
https://github.com/boostorg/beast/blob/76043dec2cf67a2ba33b32bdcc129f5f0027b8be/include/boost/beast/core/impl/basic_stream.hpp#L473  
  
However, if you are passing an iterator or a query returned from a name resolution, then it will go through an overload of `boost::asio::async_connect` which can connect to multiple addresses in sequence and in this case the socket will be first closed each time, losing the binding:  
https://github.com/boostorg/beast/blob/76043dec2cf67a2ba33b32bdcc129f5f0027b8be/include/boost/beast/core/impl/basic_stream.hpp#L547  
  
From https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/reference/async_connect/overload6.html  
  
![image](https://user-images.githubusercontent.com/1503976/185466688-de1bacba-e217-4084-90d5-a868cd7fea65.png)

---

## Comment 5

> Username: ILikeIneine  
> Created at: 2022-08-21 05:13:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1221470449  

> The devil is in the details. What are you passing to `async_connect`?   
  
Ok I got it! I passed the name solution from the query returned so it hit the second overload, and I saw that `sock_->close()` in the source.  
  
After specific the param as a concrete endpoint by myself, it works fine already. thanks!  
  
By the way, Could you recommend some articals, blogs or videos or somethings else to help me understand the architecture or familiar with the design concept of boost net part(asio and beast)? I've been stucked with these *stream layers* and the code jumped between multiple overload. I only find some cppcons and old blogs, but both are quite roughly.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-08-21 05:22:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1221471429  

> Could you recommend some articals, blogs or videos or somethings else to help me understand the architecture or familiar with the design concept of boost net part(asio and beast)?  
  
Sadly no. Almost everything that I know, I learned through painstaking trial and error. And studying Asio source code, and the examples that come with it.

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-08-21 08:54:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1221501788  

https://www.youtube.com/watch?v=D-lTwGJRx0o  
https://www.youtube.com/watch?v=wxXIbaJBZlE  
https://www.youtube.com/watch?v=icgnqFM-aY4

---

## Comment 8

> Username: ILikeIneine  
> Created at: 2022-08-21 11:33:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2505#issuecomment-1221527450  

Awesome! I'll taste it as soon, thanks very much!
