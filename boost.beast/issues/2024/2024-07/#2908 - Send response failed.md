# #2908 - Send response failed [Closed]

> Username: xiaoma565  
> Created at: 2024-07-27 08:32:28 UTC  
> Updated at: 2024-07-28 07:49:24 UTC  
> Closed at: 2024-07-28 07:49:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2908  

Sir, my http_server got a error when deal with hundreds of requests.  
  
![image](https://github.com/user-attachments/assets/c34f1da3-0fa2-4ee8-95b8-0d6f7fa1da60)  
  
The last call stack in my code is here  
![image](https://github.com/user-attachments/assets/1f2a08df-9e99-4e41-8cb2-6a6c5ee2301f)  
[http_server.zip](https://github.com/user-attachments/files/16398748/http_server.zip)  
  
  
I doubt calling async_write multiple times in class ServerConnection may trigger this error.  
Please help me.

---

## Comment 1

> Username: xiaoma565  
> Created at: 2024-07-27 08:53:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2908#issuecomment-2254077535  

[call_stack.txt](https://github.com/user-attachments/files/16398781/call_stack.txt)

---

## Comment 2

> Username: ashtum  
> Created at: 2024-07-27 09:30:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2908#issuecomment-2254092050  

You can't call `http::async_write` repeatedly without waiting for the previous operation to complete. In your application, you must either defer calling `async_read` until `async_write` is complete (which means you cannot initiate another `async_write`), or queue the responses and write them one by one. Here is an example of how to implement this: https://github.com/boostorg/beast/blob/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb/example/advanced/server/advanced_server.cpp#L412

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-07-28 07:49:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2908#issuecomment-2254381267  

> You can't call `http::async_write` repeatedly without waiting for the previous operation to complete. In your application, you must either defer calling `async_read` until `async_write` is complete (which means you cannot initiate another `async_write`), or queue the responses and write them one by one. Here is an example of how to implement this:  
>   
> https://github.com/boostorg/beast/blob/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb/example/advanced/server/advanced_server.cpp#L412  
  
Yes, I already implement this.
