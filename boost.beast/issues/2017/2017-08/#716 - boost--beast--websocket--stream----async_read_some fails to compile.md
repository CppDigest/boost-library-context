# #716 - boost::beast::websocket::stream<>::async_read_some fails to compile [Closed]

> Username: qwertzui11  
> Created at: 2017-08-03 10:36:40 UTC  
> Updated at: 2017-08-03 13:44:29 UTC  
> Closed at: 2017-08-03 12:04:46 UTC  
> Url: https://github.com/boostorg/beast/issues/716  

## compiler  
g++ (Ubuntu 6.3.0-12ubuntu2) 6.3.0 20170406  
c++14 is used  
## Os  
ubuntu 16.04  
## beast version  
master (v98)  
## code  
```  
boost::beast::websocket::stream<tcp_socket_type> stream;  
boost::beast::multi_buffer buffer;  
stream.async_read_some(buffer.prepare(512),   
    [](const auto&, auto){});  
```  
## compilation result...  
... tells me that it can't invoke the callback when an error occurs:  
```  
boost/beast/websocket/impl/fail.ipp:230:9: error: no match for call to ‘[...]::<lambda(const boost::system::error_code&, std::size_t)>) (boost::beast::error_code&)’  
         h_(ec);  
         ^~  
```  
https://github.com/boostorg/beast/blob/master/include/boost/beast/websocket/impl/fail.ipp#L230  
  
So the callback needs two parameters (error_code and size_t bytes_transferred), but if I interpreted the code right, only error_code gets passed.  
  
by the way, congrats for the acceptance to boost. I'm super happy, to see this library in the collection :grinning:

---

## Comment 1

> Username: qwertzui11  
> Created at: 2017-08-03 10:41:21 UTC  
> Url: https://github.com/boostorg/beast/issues/716#issuecomment-319934047  

the method documentation says:  
```  
@param handler The handler to be called when the read operation  
completes. Copies will be made of the handler as required. The  
equivalent function signature of the handler must be:  
@code  
void handler(  
	error_code const& ec,       // Result of operation  
	std::size_t bytes_written   // Number of bytes written to the buffer sequence  
);  
```  
and seems to enforce it with static_asserts.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-03 11:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/716#issuecomment-319939697  

le sigh... you're right. fixing...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-03 12:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/716#issuecomment-319950571  

You should be good to go with **version 99**, thanks for the report!

---

## Comment 4

> Username: qwertzui11  
> Created at: 2017-08-03 12:45:37 UTC  
> Url: https://github.com/boostorg/beast/issues/716#issuecomment-319958535  

tested and works great! thank you.  
However, I'm getting a "clang" warning for casting a signed number (`int`) to an `unsigned long` (`std::size_t`), because of my `auto` parameters. What I mean is that auto seems to deduce to int. I'm not getting that warning with gcc, with `-Wall` or `-Wextra`. It's not an issue, but I thought it's worth noting. Without `auto`, there's no warning.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-03 13:44:19 UTC  
> Updated at: 2017-08-03 13:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/716#issuecomment-319972863  

I wouldn't know about that because `auto` lambda parameters are C++14 and Beast compiles  
with `--std=c++11`
