# #2092 - is it possible to provide async_write_read function for convenience [Closed]

> Username: yaozongyou  
> Created at: 2020-09-18 08:38:29 UTC  
> Updated at: 2020-09-22 06:59:26 UTC  
> Closed at: 2020-09-22 06:59:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2092  

hi, is it possible to provide a composed operation async_write_read function for convenience. this function receives a request message as a parameter, internally, it async_write this message, and async_read response message.  Combine these into a single composed operation, it will be very convenient for common usage.  
  
Thanks~

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-18 09:28:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2092#issuecomment-694761511  

This is beyond the scope of beast, as the exact implementation will depend on the details of how the request should be checked and the response formed.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-18 09:46:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2092#issuecomment-694769964  

However, it would be fairly easy to write a composed operation to do this. We often get requests to also produce a "server framework", along the lines of nodejs express. This is also beyond the scope of beast, but could for the basis of another library that depends on beast.

---

## Comment 3

> Username: yaozongyou  
> Created at: 2020-09-18 12:14:24 UTC  
> Updated at: 2020-09-18 12:14:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2092#issuecomment-694832844  

got it, thanks for your kind reply.
