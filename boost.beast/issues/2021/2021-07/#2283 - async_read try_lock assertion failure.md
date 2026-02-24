# #2283 - async_read try_lock assertion failure [Closed]

> Username: uvguy  
> Created at: 2021-07-13 02:25:43 UTC  
> Updated at: 2023-02-27 02:56:55 UTC  
> Closed at: 2021-07-16 11:19:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2283  

I have dummy broadcast ws server here, but my first intention is when the server successfully broadcast write data then the server must read client request again. How to avoid soft_mutex try_lock assertion  
  
### Version of Beast  
  
 boost version 1.76 on linux  
  
### Steps necessary to reproduce the problem  
  
I've placed paired client and part of my server code on [my gists](https://gist.github.com/uvguy/0355caabdfc78f5233cb7df79a8a6a03#file-main-cpp-L167)   
  
### All relevant compiler information

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-07-13 10:39:00 UTC  
> Updated at: 2021-07-13 10:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2283#issuecomment-878978087  

The usual reason for this is that you've initiated an async_write before the previous one has completed, which is forbidden for any asio-style io object.

---

## Comment 2

> Username: uvguy  
> Created at: 2021-07-14 14:11:50 UTC  
> Updated at: 2021-07-14 14:28:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2283#issuecomment-879927628  

Thanks for your suggestions, but my async_write has already recursively call itself until there are no buffer queue for writing that same as multi_chat example did. Using async_read or posting asio event to trigger server async_read when buffer queue was empty still yield same problem. Is there any suggestions for additional protection state to notify when all async_write successfully end?

---

## Comment 3

> Username: C8LUKA  
> Created at: 2023-02-27 02:56:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2283#issuecomment-1445614140  

Is that problem done which show in your code @uvguy
