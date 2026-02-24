# #38 - no support for std::array [Closed]

> Username: octopus-prime  
> Created at: 2022-02-05 13:04:37 UTC  
> Updated at: 2022-02-07 04:02:51 UTC  
> Closed at: 2022-02-07 04:02:51 UTC  
> Url: https://github.com/boostorg/qvm/issues/38  

Broken:  
  
```  
    std::array<float, 4> v = {1, 2, 3, 4};  
  
    boost::qvm::vref(v); --> error: no type named ‘type’ in ‘struct boost::enable_if_c<false, boost::qvm::qvm_detail::vref_<std::array<float, 4> >&>’  
  
    boost::qvm::vref(v.data()); --> error: no type named ‘type’ in ‘struct boost::enable_if_c<false, boost::qvm::qvm_detail::vref_<float*>&>’  
```

---

## Comment 1

> Username: zajo  
> Created at: 2022-02-07 04:02:51 UTC  
> Url: https://github.com/boostorg/qvm/issues/38#issuecomment-1031048458  

Thanks. Fixed on `develop`.
