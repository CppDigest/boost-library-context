# #67 - ldxp/stxp [Closed]

> Username: cyring  
> Created at: 2024-05-09 08:21:46 UTC  
> Updated at: 2024-05-09 08:46:16 UTC  
> Closed at: 2024-05-09 08:46:16 UTC  
> Url: https://github.com/boostorg/atomic/issues/67  

Hello,  
  
https://github.com/boostorg/atomic/blob/08bd4e20338c503d2acfdddfdaa8f5e0bcf9006c/include/boost/atomic/detail/core_arch_ops_gcc_aarch64.hpp#L1730  
  
If branch is taken where `stxp` is releasing `%[storage]` ?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-05-09 08:46:16 UTC  
> Url: https://github.com/boostorg/atomic/issues/67#issuecomment-2102228367  

Nowhere. This is not a problem because the lock address will be either overridden by the next executed `ldxp` or cleared on the context switch.
