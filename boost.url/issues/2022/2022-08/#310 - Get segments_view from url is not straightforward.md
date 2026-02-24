# #310 - Get segments_view from url is not straightforward [Closed]

> Username: alandefreitas  
> Created at: 2022-08-03 00:01:51 UTC  
> Updated at: 2022-09-11 23:58:08 UTC  
> Closed at: 2022-09-11 23:58:08 UTC  
> Url: https://github.com/boostorg/url/issues/310  

Functions might require `segments_view` so that they work for `url_view` and `url`. A mutable `url_view` returns `segments_view` while a mutable `url` returns `urls::segments`.   
  
This means the `url` needs to converted to `const` or to its base class `url_view` to access the `segments_view`.  
  
Example:  
  
```cpp  
bool my_function(urls::segments_view target, urls::segments_view prefix);  
```  
  
would require:  
  
```cpp  
url_view uv = /* ... */;  
url u = /* ... */;  
my_function(  
    uv.segments(),  
    static_cast<urls::url_view>(u).segments());  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-03 00:03:18 UTC  
> Url: https://github.com/boostorg/url/issues/310#issuecomment-1203334098  

now that we no longer allocate, shouldn't they be freely convertible back and forth?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-03 01:02:26 UTC  
> Url: https://github.com/boostorg/url/issues/310#issuecomment-1203366389  

I think so.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-11 23:58:08 UTC  
> Url: https://github.com/boostorg/url/issues/310#issuecomment-1243072614  

This works:  
```  
url u( "/path/to/file.txt" );  
  
segments_view ps( u.segments() );  
```  
  
so I think we're good now
