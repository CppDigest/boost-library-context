# #390 - bug in set_path_absolute [Closed]

> Username: vinniefalco  
> Created at: 2022-08-12 04:51:26 UTC  
> Updated at: 2022-10-03 20:51:05 UTC  
> Closed at: 2022-10-03 20:51:05 UTC  
> Url: https://github.com/boostorg/url/issues/390  

```  
url u( "/kyle:xy" );  
u.set_path_absolute( false );  
assert( u.string() != "kyle:xy" );  
```  
  
fails

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-12 04:58:05 UTC  
> Updated at: 2022-08-12 04:58:39 UTC  
> Url: https://github.com/boostorg/url/issues/390#issuecomment-1212730065  

This must wait until after review, there is no point trying to rush a fix. Libraries have bugs, its normal. It is unreasonable to expect the library to be completely without bugs upon review (although it will eventually get there).
