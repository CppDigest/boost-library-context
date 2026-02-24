# #2151 - Beast samples for secured websocket are not proper [Closed]

> Username: TsvetanBogoev  
> Created at: 2021-01-22 15:34:11 UTC  
> Updated at: 2024-06-06 05:34:22 UTC  
> Closed at: 2022-01-09 05:17:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2151  

BOOST_BEAST_VERSION 189  
  
In _websocket_client_async_ssl.cpp_ a root certificate is added to the context   
  
`load_root_certificates(ctx);`  
  
 but this certificate is not used and in this sample peer certificate will not be validated without a call to set_verify_mode   
  
```  
// Verify the remote server's certificate  
ctx.set_verify_mode(ssl::verify_peer);  
```  
  
which is missing. In other samples like http_client_async_ssl.cpp the call is present

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-22 17:31:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2151#issuecomment-765572203  

Thanks for reporting.   
You are correct.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2151#issuecomment-850857898  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2151#issuecomment-1008232363  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
