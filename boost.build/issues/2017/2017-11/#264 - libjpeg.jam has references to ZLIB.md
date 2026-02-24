# #264 - libjpeg.jam has references to ZLIB [Closed]

> Username: pdimov  
> Created at: 2017-11-22 18:53:50 UTC  
> Updated at: 2017-11-28 17:21:06 UTC  
> Closed at: 2017-11-28 17:21:06 UTC  
> Url: https://github.com/boostorg/build/issues/264  

https://github.com/boostorg/build/blob/develop/src/tools/libjpeg.jam#L133  
https://github.com/boostorg/build/blob/develop/src/tools/libjpeg.jam#L149  
https://github.com/boostorg/build/blob/develop/src/tools/libjpeg.jam#L202  
  
These are presumably cut/paste mistakes.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-11-28 17:21:00 UTC  
> Url: https://github.com/boostorg/build/issues/264#issuecomment-347597201  

Fixed in https://github.com/boostorg/build/commit/5557ee142394fe5adb81b1cc36b709c2ddb7d685 as far as I can see.
