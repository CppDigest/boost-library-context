# #2541 - Is boost::beast::tcp_stream's expires_after only work for single chain of asynchronous operations ? [Closed]

> Username: xtayaitak  
> Created at: 2022-10-17 07:02:34 UTC  
> Updated at: 2022-10-17 19:33:18 UTC  
> Closed at: 2022-10-17 19:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2541  

My protocal is full duplex.  
So the async write may called before async_read is finished.  
  
can below pseudo-code code's "expires_after" also work correctly?  
  
expires_after(60)  
async_read_some(xxx)  
expire_afater(20)  
async_write(xxx)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-17 07:50:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2541#issuecomment-1280434383  

I don't know what correct means based on your brief description.

---

## Comment 2

> Username: xtayaitak  
> Created at: 2022-10-17 08:05:31 UTC  
> Updated at: 2022-10-17 08:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2541#issuecomment-1280451156  

@klemens-morgenstern   
I'm not sure if expires_after can be called repeatedly.when previous expires_after async  operation has not finished.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-17 09:17:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2541#issuecomment-1280543651  

Thanks for the clarification. yes it can.
