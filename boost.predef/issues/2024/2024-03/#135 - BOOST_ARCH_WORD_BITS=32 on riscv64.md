# #135 - BOOST_ARCH_WORD_BITS=32 on riscv64 [Open]

> Username: Kojoley  
> Created at: 2024-03-24 17:29:30 UTC  
> Updated at: 2024-03-24 17:29:30 UTC  
> Url: https://github.com/boostorg/predef/issues/135  

This logic is obviously wrong:  
https://github.com/boostorg/predef/blob/614546d6fac1e68cd3511d3289736f31d5aed1eb/include/boost/predef/architecture/riscv.h#L29-L41  
IIUC `__riscv_xlen` can be used to detect correct word bitwidth.
