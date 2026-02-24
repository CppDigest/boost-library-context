# #195 - Cannot properly build boost for mips n64 - Boost.Context update breaks support [Closed]

> Username: ClaymorePT  
> Created at: 2022-01-09 03:10:08 UTC  
> Updated at: 2023-08-18 11:17:13 UTC  
> Closed at: 2023-08-18 05:10:43 UTC  
> Url: https://github.com/boostorg/context/issues/195  

Hello!  
  
It seems that with the update from 1.77 to 1.78, Boost.Context mips support has unified all mips supported architectures, changing the target name `mips1` to `mips`. This work was not done for Boost build configuration and Boost.Atomic.  
  
This issue was discovered with the Boost library update in OpenWRT done here https://github.com/openwrt/packages/pull/17512 where the issue was analysed and resolved for OpenWRT.

---

## Comment 1

> Username: olk  
> Created at: 2023-08-18 05:10:43 UTC  
> Url: https://github.com/boostorg/context/issues/195#issuecomment-1683361438  

should work now

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2023-08-18 11:17:12 UTC  
> Url: https://github.com/boostorg/context/issues/195#issuecomment-1683764288  

Confirmed!
