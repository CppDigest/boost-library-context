# #21 Add a static_cast to allow strict type conversion errors [Merged]

> Username: zdewitt  
> Created at: 2023-06-02 22:50:59 UTC  
> Updated at: 2023-09-27 18:24:18 UTC  
> Merged at: 2023-09-27 18:24:18 UTC  
> Closed at: 2023-09-27 18:24:18 UTC  
> Url: https://github.com/boostorg/crc/pull/21  

When compiling with -Wconversion enabled, there is an implicit narrowing here when Unsigned type is a char and is promoted to an int during these arithmetic operations. Use a cast to resolve this

---
