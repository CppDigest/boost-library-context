# #2942 - example 'advanced_server' crash when in debug [Closed]

> Username: zozobreakzou  
> Created at: 2024-10-23 01:46:27 UTC  
> Updated at: 2024-11-19 11:35:01 UTC  
> Closed at: 2024-11-19 11:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2942  

When I set a breakpoint in the "handle_request" function to debug the example "advanced_server",  if I paused it much time more than 30 seconds, programe will crash with a bad_executor exception. I can reproduce it by just sleep 40 seconds in 'handle_request'. Is this a beast issue or asio issue? @bachase   
  
![Image](https://github.com/user-attachments/assets/b5ee9c9e-b326-49a0-a296-dff0689ee780)  
  
![Image](https://github.com/user-attachments/assets/c7b43807-e93b-4f06-800a-010add8a59a3)  
  
My environment  
boost: 1.86.0  
compiler: visual stuido 2022 community  
launch params: 0.0.0.0 8080 . 1

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-10-23 04:18:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2942#issuecomment-2430848781  

is this using the `beast::tcp_stream` ?

---

## Comment 2

> Username: ashtum  
> Created at: 2024-10-23 05:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2942#issuecomment-2430951345  

@zozobreakzou , I believe this is a duplicate of https://github.com/boostorg/beast/issues/2925, which has already been fixed and will be included in the Boost 1.87 release.

---

## Comment 3

> Username: zozobreakzou  
> Created at: 2024-10-23 09:37:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2942#issuecomment-2431504564  

> is this using the `beast::tcp_stream` ?  
  
Yes, just as the advanced_server is, nothing changed.
