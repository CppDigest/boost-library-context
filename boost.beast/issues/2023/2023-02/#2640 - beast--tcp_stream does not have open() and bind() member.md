# #2640 - beast::tcp_stream does not have open() and bind() member [Closed]

> Username: chivas1000  
> Created at: 2023-02-17 03:34:26 UTC  
> Updated at: 2023-10-28 16:31:23 UTC  
> Closed at: 2023-10-28 16:31:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2640  

Hi there,  
I am rewriting a websocket.h  
I want to specify(bind) the local endpoint(Local IP and port) of the websocket,   
by referencing  
https://github.com/boostorg/beast/issues/2090  
I've manage to do this if I use this kind of socket  
`websocket::stream<tcp::socket> ws_;`  
  
but for this  
`websocket::stream <beast::tcp_stream> ws_;`  
  
the   
  
        beast::get_lowest_layer(ws_).open(boost::asio::ip::tcp::v4());  
        beast::get_lowest_layer(ws_).bind(tcp::endpoint(boost::asio::ip::make_address_v4(specify_local_address), specify_local_portnumber));  
are not useful because it said  
"No member named 'open' in 'boost::beast::basic_stream<boost::asio::ip::tcp>"  
"No member named 'bind' in 'boost::beast::basic_stream<boost::asio::ip::tcp>"  
  
but actually I need  
`websocket::stream <beast::tcp_stream> ws_;`  
because it has timeouts and a polymorphic executor, which is needed for this websocket.h following code and its application  
  
has any one know what to do?  
Any help and assistance would be appreciated  
Thanks.  
  
  
  
version:  boost-1.70.0  
  
steps to reproduce:  
  
    class session : public std::enable_shared_from_this<session> {  
      
        websocket::stream <beast::tcp_stream> ws_;  
    //    beast::tcp_stream ws_;  
        beast::flat_buffer buffer_;  
        std::vector<std::shared_ptr<const std::string>> queue;  
        std::string host_;  
        std::function<void(const std::string&)> on_message;  
      
    public:  
        // Resolver and socket require an io_context  
        explicit  
        session(net::io_context &ioc, std::function<void(const std::string&)> on_message)  
    //    I might not need a resolver since itself initialize a connection  
    //            : resolver_(net::make_strand(ioc)), ws_(net::make_strand(ioc)), on_message(std::move(on_message)) {  
    //    but I may initialize a websocket here  
        : ws_(net::make_strand(ioc)), on_message(std::move(on_message))  
        {  
            string  specify_local_address = "0.0.0.0";  
            unsigned short  specify_local_portnumber = 46675;  
      
           //Here I got errors  
            beast::get_lowest_layer(ws_).open(boost::asio::ip::tcp::v4());  
            beast::get_lowest_layer(ws_).bind(tcp::endpoint(boost::asio::ip::make_address_v4(specify_local_address), specify_local_portnumber));  
      
      
        }

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-02-17 04:09:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2640#issuecomment-1434069898  

```cpp  
    beast::get_lowest_layer(ws_).socket().open(boost::asio::ip::tcp::v4());  
    beast::get_lowest_layer(ws_).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4(specify_local_address), specify_local_portnumber));  
```  
  
Will give you access to the underlying tcp socket.   
  
You also might want to consider replacing `beast::tcp_stream` with `asio::ip::tcp::socket` if you want to this manually. The `beast::tcp_stream` does a bunch of things automatically for you, that you might not want if you do that much stuff manually.

---

## Comment 2

> Username: chivas1000  
> Created at: 2023-02-17 05:09:08 UTC  
> Updated at: 2023-02-17 05:50:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2640#issuecomment-1434109628  

> ```c++  
>     beast::get_lowest_layer(ws_).socket().open(boost::asio::ip::tcp::v4());  
>     beast::get_lowest_layer(ws_).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4(specify_local_address), specify_local_portnumber));  
> ```  
>   
> Will give you access to the underlying tcp socket.  
>   
> You also might want to consider replacing `beast::tcp_stream` with `asio::ip::tcp::socket` if you want to this manually. The `beast::tcp_stream` does a bunch of things automatically for you, that you might not want if you do that much stuff manually.  
  
It works, thanks for your help!  
Since I am modifying the original code and I am relatively new to socket, so I might not change the socket if  I as long as I could specify local IP and Port  
  
Or is there any suggestions on how to use given beast api to bind a local endpoint without manually control underlying websocket?  
  
Or since I've seen in an issue said that by opening an resolver it would automatically shuts the previous websocket and start a new one, by rewriting what member function could I change this behavior?   
  
Again, thanks for your quick and helpful response!

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:09:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2640#issuecomment-1455257754  

I don't understand the first question and the second is your design choice.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-19 05:54:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2640#issuecomment-1684841810  

@chivas1000 Is this still open?
