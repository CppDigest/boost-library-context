# #2182 - websocket client socket types difference [Closed]

> Username: Tectu  
> Created at: 2021-03-07 12:19:53 UTC  
> Updated at: 2021-03-08 18:40:10 UTC  
> Closed at: 2021-03-08 18:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2182  

I am referring to `example/websocket/client/sync` and `example/websocket/client/async`.  
  
The `sync` example uses `websocket::stream<tcp::socket>`.  
The `async` example uses `websocket::stream<beast::tcp_stream>`.  
  
I would like to understand the difference and especially why then can't both operate on the same type (eg. `boost::beast::tcp_stream`.  
  
`beast::tcp_stream` is defined as:  
```cpp  
using tcp_stream = basic_stream<  
    net::ip::tcp,  
    net::any_io_executor,  
    unlimited_rate_policy>;  
```  
  
In order to understand this better I attempted to modify the `sync` client example to use `beast::tcp_stream` but then the endpoint look-up doesn't compile:  
```cpp  
auto ep = net::connect(ws.next_layer(), results);  
```  
  
Questions:  
  
1. Why are the websocket types different between the `sync` and `async` client examples?  
2. Is there a way to make the `sync` client example work with `websocket::stream<beast::tcp_stream>`?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-07 14:23:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2182#issuecomment-792287218  

1. The beast::tcp_stream adds a rate-limit and in-built timeouts on reads and writes on top of the functionality of the underlying socket type. These extra functions are only available when the stream's asynchronous functions are used.  
  
2. Yes, it will work, but you realise no benefit from the tcp_stream since it's using the synchronous functions. The synchronous functions are simple pass-throughs.

---

## Comment 2

> Username: Tectu  
> Created at: 2021-03-08 18:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2182#issuecomment-792981963  

Thank you for the clarification.
