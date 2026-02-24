# #215 - Request must outlive async_exec? [Closed]

> Username: bruno-viva  
> Created at: 2024-10-11 19:39:15 UTC  
> Updated at: 2025-06-10 10:44:02 UTC  
> Closed at: 2025-06-10 10:44:02 UTC  
> Url: https://github.com/boostorg/redis/issues/215  

First of all, thanks for this amazing library! It makes it very easy to integrate with Redis. Now onto the issue:  
  
When calling `async_exec`, it is taking the request and keeping a pointer to it (in `req_info`). So a code like this:  
  
```cpp  
void SetKey(std::string_view key, std::string_view value, boost::redis::connection& connection) {  
  boost::redis::request request;  
  request.push("SET", key, value);  
  auto response = std::make_unique<boost::redis::response<std::string>>();  
  auto& response_ref = *response;  
  connection.async_exec(  
      request, response_ref,  
      [response = std::move(response)](const boost::system::error_code& error, std::size_t) {  
        if (error) {  
          std::cout << "Failed to send SET command to Redis server: " << error.what() << std::endl;  
        } else {  
          std::cout << "Response: " << std::get<0>(*response).value() << std::endl;  
        }  
      });  
  // **Boom, request goes out of scope.**  
}  
```  
  
Is invalid and has undefined behavior. I am using C++17, and so I prefer to not try the coroutines version.  
  
I am not sure what the correct fix to the library is here (Should probably keep a copy of the request? Have const request& and request&& alternatives to copy/move the request in?). But I think the documentation could mention that, as it was not immediately obvious. In other words, the response is kind of obvious that needs to outlive it, but not the request! Some more C++17 examples would help too.  
  
For completeness, here is a working version:  
  
```cpp  
void SetKey(std::string_view key, std::string_view value, boost::redis::connection& connection) {  
  auto request = std::make_unique<boost::redis::request>();  
  request->push("SET", key, value);  
  auto response = std::make_unique<boost::redis::response<std::string>>();  
  auto& request_ref = *request;  
  auto& response_ref = *response;  
  connection.async_exec(  
      request_ref, response_ref,  
      [request = std::move(request), response = std::move(response)](const boost::system::error_code& error, std::size_t) {  
        if (error) {  
          std::cout << "Failed to send SET command to Redis server: " << error.what() << std::endl;  
        } else {  
          std::cout << "Response: " << std::get<0>(*response).value() << std::endl;  
        }  
      });  
}  
```  
  
Thanks again for this lib! Looking forward to hearing your thoughts on this.

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-10-12 10:17:49 UTC  
> Url: https://github.com/boostorg/redis/issues/215#issuecomment-2408510245  

Hi Bruno. Yes, you have to guarantee that the request and response outlives the async operation, this is usually achieved with a `shared_ptr`, for example  
  
```cpp  
auto request = std::make_shared<boost::redis::request>();  
request->push("SET", key, value);  
auto response = std::make_shared<boost::redis::response<std::string>>();  
  
connection.async_exec(*request, *response, [request, response](auto const& error, std::size_t) {  
     ...  
});  
```  
  
Alternatively you can also have a look at the my [cpp17_intro_sync.cpp](https://github.com/boostorg/redis/blob/dbfb72d8534e932bc4992fb894e3bc7a4007901c/example/cpp17_intro_sync.cpp) example. The code above would look like this in this case  
  
```cpp  
sync_connection conn;  
conn.run(cfg);  
  
request req;  
request->push("SET", key, value);  
response<std::string> resp;  
  
conn.exec(req, resp);  
conn.stop();  
```  
  
The sync connection type is not part of the library but provided as an example in [sync_connection.hpp](https://github.com/boostorg/redis/blob/dbfb72d8534e932bc4992fb894e3bc7a4007901c/example/sync_connection.hpp), which you can copy to your project.

---

## Comment 2

> Username: bruno-viva  
> Created at: 2024-10-12 22:53:06 UTC  
> Url: https://github.com/boostorg/redis/issues/215#issuecomment-2408723626  

Thanks Marcelo! It makes sense now. Do you think the API docs should state that or maybe the API should be modified to make it clearer?  
  
It isn't immediately obvious when reading the function definition:  
  
`auto async_exec(request const& req, Response& resp, CompletionToken token)`  
  
`request` seems cheap to move, and could be moved in to `req_info`, instead of it being a pointer. Was there a reason on why it was kept as a pointer? Thanks in advance

---

## Comment 3

> Username: anarthal  
> Created at: 2024-12-22 21:34:49 UTC  
> Url: https://github.com/boostorg/redis/issues/215#issuecomment-2558609110  

Without knowing the actual reasons, I'd speculate that this pattern allows to reuse request objects, while moving them wouldn't.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-02-20 11:29:43 UTC  
> Url: https://github.com/boostorg/redis/issues/215#issuecomment-2671226024  

> request seems cheap to move, and could be moved in to req_info, instead of it being a pointer. Was there a reason on why it was kept as a pointer?  
  
What @anarthal said is correct, in some cases like [here](https://github.com/boostorg/redis/blob/ac4e6e29b372783b9c11b015ef187f07f2112e46/example/cpp20_subscriber.cpp#L53)  we want to reuse the `request`. In other cases, such as [here](https://github.com/boostorg/redis/blob/ac4e6e29b372783b9c11b015ef187f07f2112e46/example/cpp20_echo_server.cpp#L39) we discard the request but reuse the internal buffer to avoid further allocation.
