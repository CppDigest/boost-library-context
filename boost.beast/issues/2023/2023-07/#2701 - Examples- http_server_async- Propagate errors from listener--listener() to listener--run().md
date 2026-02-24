# #2701 - Examples: http_server_async: Propagate errors from listener::listener() to listener::run() [Closed]

> Username: ceggers-arri  
> Created at: 2023-07-06 09:49:13 UTC  
> Updated at: 2024-01-08 13:50:58 UTC  
> Closed at: 2024-01-08 13:50:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2701  

If the constructor fails to establish a listening socket (e.g. `::bind()` returns `EADDRINUSE`),  
only an error message is printed (but the error is not communicated to the caller).   
  
```  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
[...]  
public:  
    listener([...])  
    {  
        beast::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;   <-- here  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
        if(ec)  
        {  
            fail(ec, "set_option");  
            return;   <-- and here  
        }  
  
        // Bind to the server address  
        acceptor_.bind(endpoint, ec);  
        if(ec)  
        {  
            fail(ec, "bind");  
            return;   <-- and so on  
        }  
 ```  
  
Although the constructor has failed, `listener::run()` is called then:  
```  
[...]  
    // Create and launch a listening port  
    std::make_shared<listener>(  
        ioc,  
        tcp::endpoint{address, port},  
        doc_root)->run();  
[...]  
```  
  
So here `accept()` is called with a non operational socket:  
  
```  
    // Start accepting incoming connections  
    void  
    run()  
    {  
        do_accept();  
    }  
  
    void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec, tcp::socket socket)  
    {  
        if(ec)  
        {  
---- We shouldn't get here, as the initial error has already been detected much earlier ---  
            fail(ec, "accept");  
            return; // To avoid infinite loop  
        }  
```  
  
I see two options:  
  
1. Throw exceptions in `listener::listener`  
a. Use throwing overloads of `open`, `set_option`, `bind`, ...  
b. Manually throw after printing the error message  
2. Record the error within the constructor and check for error in `run`  
a. By using an additional error member  
b. By closing the acceptor on error and checking whether it is open in `run`

---

## Comment 1

> Username: ceggers-arri  
> Created at: 2023-07-06 11:26:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2701#issuecomment-1623506952  

Related to #2319  
  
In my project I had to revert the changes from #2319, because it looks like `accept()` can also fail for other reasons, which are non-permanent (is there something like a "spurious accept"?) I saw such a behavior when scanning my system with NMAP. Due to the `return` statement after a failed `accept`, the listener is destroyed.  
  
The error handling should distinguish between permanent errors (e.g. `bind` failed) and non-permanent errors (a single `accept` failed).

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-01 12:38:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2701#issuecomment-1873310994  

@ceggers-arri, it is correct that errors in the constructor of the listener won't prevent the execution of `run()`. However, calling `async_accept` on a failed acceptor should complete with an "invalid argument" error. Therefore, it is not expected to hang in an infinite loop. Are you experiencing a different result?
