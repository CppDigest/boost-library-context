# #2450 - Zip64 with zlib [Closed]

> Username: ghost  
> Created at: 2022-06-15 01:19:36 UTC  
> Updated at: 2022-06-15 01:22:52 UTC  
> Closed at: 2022-06-15 01:21:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2450  

Hello!!  
  
I was wondering if the [zlib](https://github.com/boostorg/beast/tree/develop/include/boost/beast/zlib) library included in this repository supports Zip64??  
  
### Version of Beast  
Latest version: [boost-1.79.0](https://github.com/boostorg/beast/releases/tag/boost-1.79.0)  
  
Regards

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-15 01:20:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2450#issuecomment-1155872080  

It does not. In fact it doesn't even support zip archives, because the version of zlib in Beast implements the _deflate_ algorithm and not the wrapper that surrounds it. This is the component necessary for compressed websocket messages. I suppose the name zlib is misleading.

---

## Comment 2

> Username: ghost  
> Created at: 2022-06-15 01:21:54 UTC  
> Updated at: 2022-06-15 01:22:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2450#issuecomment-1155872488  

Thanks for your quick reply. I will close this issue 😉
