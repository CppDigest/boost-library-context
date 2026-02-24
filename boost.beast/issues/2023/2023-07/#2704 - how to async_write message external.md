# #2704 - how to async_write message external? [Closed]

> Username: clee01  
> Created at: 2023-07-12 16:09:30 UTC  
> Updated at: 2023-07-17 05:33:38 UTC  
> Closed at: 2023-07-17 05:33:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2704  

In [websocket_client_async.cpp](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp)  
  
if i want to call async_write serveral times in main process, how to re-design this example code?  
  
something probably should keep in mind:  
* make io_context strand  
* ensure session working properly before async_write being invoked  
* ...

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-13 06:59:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2704#issuecomment-1633673967  

You don't redesign it, you call async_write again from on_write. If you don't like callbacks you can use another completion model.

---

## Comment 2

> Username: clee01  
> Created at: 2023-07-13 08:55:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2704#issuecomment-1633838003  

> You don't redesign it, you call async_write again from on_write. If you don't like callbacks you can use another completion model.  
  
but the param `buffer` need to async_write i have no idea in Session, i only know it out of the Session.  
so that's my intention to `call async_write serveral times in main process`

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-07-13 09:42:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2704#issuecomment-1633912815  

I don't understand what you want to do.

---

## Comment 4

> Username: clee01  
> Created at: 2023-07-13 10:02:20 UTC  
> Updated at: 2023-07-13 11:00:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2704#issuecomment-1633942763  

In [websocket_client_async.cpp](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp) make it simple as below  
```cpp  
class session { ... };  
  
int main() {  
  ...  
  // Launch the asynchronous operation  
  std::make_shared<session>(ioc)->run(host, port, text);  
  
  // Run the I/O service. The call will return when  
  // the socket is closed.  
  std::thread th([&]() { ioc.run(); });  
  ...  
  
  GetSession()->async_write(...);  
}  
```  
  
* GetSession()->async_write(...);  
  
probably used like above
