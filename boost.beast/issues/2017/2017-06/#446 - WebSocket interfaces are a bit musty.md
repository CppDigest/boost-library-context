# #446 - WebSocket interfaces are a bit musty [Closed]

> Username: vinniefalco  
> Created at: 2017-06-08 23:43:07 UTC  
> Updated at: 2017-06-10 06:11:50 UTC  
> Closed at: 2017-06-10 06:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/446  

The WebSocket code hasn't been touched in quite a few months. On a positive note, there haven't been any bugs (especially in the zlib or permessage-deflate). However, upon reviewing the documentation its clear that the `stream` interfaces are in need of a spring cleaning.
