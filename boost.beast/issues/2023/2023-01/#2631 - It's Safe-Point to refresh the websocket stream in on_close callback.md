# #2631 - It's Safe-Point to refresh the websocket stream in on_close callback [Closed]

> Username: g302ge  
> Created at: 2023-01-25 22:28:49 UTC  
> Updated at: 2023-01-29 02:34:43 UTC  
> Closed at: 2023-01-29 02:34:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2631  

I have a self-wrapped websocket lib work with beast. like below  
  
```c++  
  
int main() {  
     asio::io_context io_context;  
     asio::work_guard<> guard(io_context.get_executor);  
     auto thread = std::thread([&io_context](){ io_context.run(); });  
     std::unique_ptr<beast::websocket::stream> stream = std::make_unique_ptr<>(io_context.get_executor());  
     stream.async_write("Some Message");  
     stream.async_close([&stream](..){   
        // refresh the stream   
        // maybe simply using the operator=(std::unique_ptr&&)  
     });  
     stream.async_write("Some Message");  
     thread.join();  
}  
```  
  
Could tell me, if the refreshing logic in close callback is safe ? In other words, my target is restarting this session in the closed callback

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-26 06:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2631#issuecomment-1404609996  

In this case yes, because you join before stream goes out of scope after everything is done.  
  
I would however recommend you just use shared_ptr and pass that instead of s reference to avoid a long list of mean bugs.

---

## Comment 2

> Username: g302ge  
> Created at: 2023-01-26 09:01:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2631#issuecomment-1404713710  

> In this case yes, because you join before stream goes out of scope after everything is done.  
>   
> I would however recommend you just use shared_ptr and pass that instead of s reference to avoid a long list of mean bugs.  
  
In my real code, I have use a callback to callabck on_close which make sure the close in single thread
