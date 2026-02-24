# #2413 - session destructor not getting invoked consistently [Closed]

> Username: RavikumarTulugu  
> Created at: 2022-04-24 16:04:13 UTC  
> Updated at: 2022-04-28 04:53:33 UTC  
> Closed at: 2022-04-28 04:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2413  

we have an async ssl server. every thing works fine. what we are seeing is that there is no consistency on the session destructor getting invoked. it randomly gets invoked. Our session object is pretty huge and it can quickly bring down our server. Wonder any one observed/reported this already . Please advise.   
Thanks

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-24 17:12:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2413#issuecomment-1107881269  

You have undefined behaviour. Most likely a race condition or something surviving longer than you think holding on to a shared_ptr.  
  
I have limited information as I only have the above paragraph to go on.  
  
My suggesting would be to think about reducing the "pretty huge" session object into more composable pieces.  
You may want to look at handler tracking (see boost asio docs) and logging object lifetimes.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2022-04-28 04:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2413#issuecomment-1111744455  

closing this as not seeing this anymore.
