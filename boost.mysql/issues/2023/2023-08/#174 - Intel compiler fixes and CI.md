# #174 - Intel compiler fixes and CI [Open]

> Username: anarthal  
> Created at: 2023-08-09 11:13:47 UTC  
> Updated at: 2023-08-09 11:14:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/174  

Build fails with  
  
```  
/home/ruben/workspace/boost-root/libs/mysql/include/boost/mysql/impl/internal/protocol/deserialize_text_field.ipp:95:35: error: explicit comparison with infinity in fast floating point mode [-Werror,-Wtautological-constant-compare]  
    if (!ok || std::isnan(val) || std::isinf(val))  // SQL std forbids these values  
```  
  
When building with the newest icpx.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-08-09 11:14:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/174#issuecomment-1671131540  

Raised by #173
