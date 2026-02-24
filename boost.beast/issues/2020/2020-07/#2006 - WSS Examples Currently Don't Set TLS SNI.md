# #2006 - WSS Examples Currently Don't Set TLS SNI [Closed]

> Username: greenbagels  
> Created at: 2020-07-04 14:59:54 UTC  
> Updated at: 2024-02-14 15:31:59 UTC  
> Closed at: 2024-02-14 15:31:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2006  

Currently the WebSocket Secure examples (I only checked the sync/async client examples) don't set the TLS SNI on the `ssl_stream` layer via the `SSL_set_tlsext_host_name` function. This leads to errors in some handshakes, such as the necropost in issue #1702   
  
Adding the following snippet should help with most users' use cases, I think:  
  
```  
// my_stream is a beast::websocket::stream  
if (!SSL_set_tlsext_host_name(my_stream.next_layer().native_handle(), host))  
{  
    // ...  
}  
```  
  
Maybe we could also inform users of https://en.wikipedia.org/wiki/Server_Name_Indication#Security_implications in the example, though maybe it's out-of-scope to teach people the finer points of TLS?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2020-08-08 07:37:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2006#issuecomment-670839527  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-08-08 10:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2006#issuecomment-670855509  

There is a fix for this issue in the pipeline.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-09-07 12:20:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2006#issuecomment-688289278  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2006#issuecomment-1256867736  

@madmongo1 where can I track that progress?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-02-14 15:31:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2006#issuecomment-1944071179  

Addressed in https://github.com/boostorg/beast/pull/2019.
