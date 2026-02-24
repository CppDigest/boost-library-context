# #431 Fix Apple clang build warning/error on iOS/tvOS [Open]

> Username: biodranik  
> Created at: 2024-04-10 10:27:03 UTC  
> Updated at: 2024-04-10 10:27:03 UTC  
> Url: https://github.com/boostorg/asio/pull/431  

```  
boost/asio/ip/impl/address_v6.ipp:285:28: error: implicit conversion loses integer precision: 'unsigned long' to 'scope_id_type' (aka 'unsigned int') [-Werror,-Wshorten-64-to-32]  
```

---
