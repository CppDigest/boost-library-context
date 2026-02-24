# #1756 - Way to reduce compression on duplicate server messages [Open]

> Username: qyangdu  
> Created at: 2019-11-05 06:59:34 UTC  
> Updated at: 2019-11-06 03:21:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1756  

### Version of Beast  
  
266  
  
### Steps necessary to reproduce the problem  
  
### All relevant compiler information  
  
Hi, as a Websocket server, there is need to publish large amount of the same messages to different clients. If we use permessage-deflate option, based on current Beast APIs there is no way to avoid repeatedly compressing the same messages at server side. Is it possible that Beast expose some APIs to allow server to cache/fetch compressed messages? or is there any other better way to handle it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-05 13:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1756#issuecomment-549812832  

There's no way to do that now, but it is something that I might consider in the future.
