# #96 - read access violation:  "this" is nullptr [Open]

> Username: Dexus  
> Created at: 2025-04-14 13:31:16 UTC  
> Updated at: 2025-04-14 13:31:16 UTC  
> Url: https://github.com/boostorg/polygon/issues/96  

https://github.com/boostorg/polygon/blob/develop/include/boost/polygon/detail/polygon_arbitrary_formation.hpp#L1028  
  
I don't know where the error is coming from now, I'm building on Windows 11 with MSVC 2022 Community as soon as I build it and run it, my debug program tells me that the line for "this" is nullptr should be responsible. However, it has run on all systems so far without any major problems, at least I hadn't been able to detect any before.
