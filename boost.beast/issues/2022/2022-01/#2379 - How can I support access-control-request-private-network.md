# #2379 - How can I support access-control-request-private-network [Closed]

> Username: markus-bonk  
> Created at: 2022-01-17 10:18:45 UTC  
> Updated at: 2022-01-17 15:08:34 UTC  
> Closed at: 2022-01-17 15:08:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2379  

Current beast version 290  
  
Google is adding a new security feature to Chrome 98: public sites trying to access devices on the local network can only do so if explicitly allowed using the pre-flight request access-control-request-private-network.  
  
How can I add support to my https/websocket server to handle the requests? The public page/server queries a local https server running on localhost to resolve the websocket server port etc.  
  
I have had a look at field.hpp and the corresponding enum value is not defined there. Are there plans to add access-control-request-private-network and what is time line?  
  
Thanks  
Markus

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-17 12:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2379#issuecomment-1014484373  

If you’re talking about adding a custom header field, you can do that now just by indexing the field with a string rather than the enum.  
  
is there more to it than this?

---

## Comment 2

> Username: markus-bonk  
> Created at: 2022-01-17 13:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2379#issuecomment-1014516630  

Thanks, I missed the indexing by string. I'll grab the chrome beta, see what happens and report back.

---

## Comment 3

> Username: markus-bonk  
> Created at: 2022-01-17 15:08:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2379#issuecomment-1014644856  

You are right. It is as simple as indexing the field using a string. Thanks
