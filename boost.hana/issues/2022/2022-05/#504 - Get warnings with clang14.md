# #504 - Get warnings with clang14 [Closed]

> Username: prudhomm  
> Created at: 2022-05-25 06:48:54 UTC  
> Updated at: 2022-10-21 02:28:59 UTC  
> Closed at: 2022-10-21 02:28:59 UTC  
> Url: https://github.com/boostorg/hana/issues/504  

I now get this warning a lot  
  
**fast_and**  
```  
 /scratch/prudhomm/feelpp/feelpp/contrib/boost/hana/include/boost/hana/detail/fast_and.hpp:21:50: warning: left operand of comma operator has no effect [-Wunused-value]  
[build]         : std::is_same<fast_and<b...>, fast_and<(b, true)...>>  
```  
  
**make**  
```  
[build] /scratch/prudhomm/feelpp/feelpp/contrib/boost/hana/include/boost/hana/core/make.hpp:35:28: warning: left operand of comma operator has no effect [-Wunused-value]  
[build]             static_assert((sizeof...(X), false),  
[build]                            ^~~~~~~~~~~~  
```  
any idea how to remove this ?

---

## Comment 1

> Username: prudhomm  
> Created at: 2022-05-26 05:12:46 UTC  
> Url: https://github.com/boostorg/hana/issues/504#issuecomment-1138160828  

this seems to be a duplicate of #503 and #502

---

## Comment 2

> Username: ldionne  
> Created at: 2022-10-21 02:28:59 UTC  
> Url: https://github.com/boostorg/hana/issues/504#issuecomment-1286376961  

This should be fixed now.
