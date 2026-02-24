# #2871 - boost::beast::http::async_read core dumped when reading chunked response [Closed]

> Username: xiaoma565  
> Created at: 2024-05-25 03:08:39 UTC  
> Updated at: 2024-05-25 13:39:43 UTC  
> Closed at: 2024-05-25 13:39:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2871  

in class ClientConnection  
![image](https://github.com/boostorg/beast/assets/21019320/84c1f5a3-b989-4f17-8637-f215a5049a7d)  
It has a variable "boost::beast::http::parser<false, boost::beast::http::empty_body> p_"  
  
ClientConnection function "Start" call ClientConnection function "DoReadHeader"  
  
![image](https://github.com/boostorg/beast/assets/21019320/753f97d3-2c57-48b1-8999-db349a519990)  
  
In function "OnReadHeader"  
I call "on_chunk_header" and "on_chunk_body"  
![image](https://github.com/boostorg/beast/assets/21019320/26c3286b-9af9-4b82-b2b6-d1de96171dd8)  
![image](https://github.com/boostorg/beast/assets/21019320/b320a02a-8f51-44ef-bad7-2f88a0dcc5c8)  
  
And then running "boost::beast::http::async_read"  
![image](https://github.com/boostorg/beast/assets/21019320/717d3fc3-80d9-4359-971b-4e43fe40f361)  
  
function "ParserChunkHeader" seem like  
![image](https://github.com/boostorg/beast/assets/21019320/7d00973f-f5eb-4e9e-9367-8e5e18dd12e6)  
  
function "ParserChunkBody"  
![image](https://github.com/boostorg/beast/assets/21019320/4369cf0f-9286-4b47-b7a4-8a7e050b3d5b)  
  
in "OnChunkBody", when p_ is done, close the socket, else keep reading  
![image](https://github.com/boostorg/beast/assets/21019320/ee090e2e-7673-40d5-bf64-260b621ec45c)  
  
when I run the program  
![image](https://github.com/boostorg/beast/assets/21019320/3b06ed5d-0e51-4e2a-9e61-77b3d0982aa2)  
  
run in gdb  
![image](https://github.com/boostorg/beast/assets/21019320/6c9fd14c-f9cf-4893-8a50-6c219000a70c)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-05-25 05:27:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2871#issuecomment-2130785269  

[http::parser::on_chunk_header](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__parser/on_chunk_header.html) stores a reference to the provided callback object. Therefore, you need to extend the lifetime of the `headerCB` and `bodyCB` variables by making them members of your class.  
  
The reason for storing references is that most `std::function` implementations utilize small buffer optimization, meaning storing a reference won't require a memory allocation. A better alternative would be to accept `std::function_ref`, but that's a C++26 feature.

---

## Comment 2

> Username: xiaoma565  
> Created at: 2024-05-25 08:38:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2871#issuecomment-2131132276  

> [http::parser::on_chunk_header](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__parser/on_chunk_header.html) stores a reference to the provided callback object. Therefore, you need to extend the lifetime of the `headerCB` and `bodyCB` variables by making them members of your class.  
>   
> The reason for storing references is that most `std::function` implementations utilize small buffer optimization, meaning storing a reference won't require a memory allocation. A better alternative would be to accept `std::function_ref`, but that's a C++26 feature.  
  
thanks so much! It is helpful. But I have got another problem.  
In my http server, I read a request and send some chunked messages. When I send the last chunk  
```c++  
boost::asio::async_write(stream_, http::make_chunk_last(), [this](boost::system::error_code ec, std::size_t bytes_transferred) {  
    do_read();  
});  
```   
I call do_read to read a new reuqest.  
```c++  
void do_read()  
{  
        req_ = {};  
  
        // Set the timeout.  
        stream_.expires_after(std::chrono::seconds(30));  
  
        // Read a request  
        http::async_read(stream_, buffer_, req_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
}  
  
void on_read(beast::error_code ec, std::size_t bytes_transferred)  
{  
        boost::ignore_unused(bytes_transferred);  
  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            return do_close();  
  
        if(ec)  
            return fail(ec, "read");  
        handle_request(*doc_root_, std::move(req_));  
        send_chunked();  
}  
```   
  
I did it with reference to this example: https://www.boost.org/doc/libs/1_85_0/libs/beast/example/http/server/async/http_server_async.cpp  
  
But I got an error:  **free(): double free detected in tcache 2**

---

## Comment 3

> Username: ashtum  
> Created at: 2024-05-25 12:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2871#issuecomment-2131236232  

```C++  
boost::asio::async_write(stream_, http::make_chunk_last(), [this](boost::system::error_code ec, std::size_t bytes_transferred) {  
    do_read();  
});  
```  
Here, you capture the `this` pointer instead of a shared pointer to your object. You are probably facing a lifetime issue as a result.

---

## Comment 4

> Username: xiaoma565  
> Created at: 2024-05-25 13:39:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2871#issuecomment-2131271974  

> ```c++  
> boost::asio::async_write(stream_, http::make_chunk_last(), [this](boost::system::error_code ec, std::size_t bytes_transferred) {  
>     do_read();  
> });  
> ```  
>   
> Here, you capture the `this` pointer instead of a shared pointer to your object. You are probably facing a lifetime issue as a result.  
  
thanks bro!
