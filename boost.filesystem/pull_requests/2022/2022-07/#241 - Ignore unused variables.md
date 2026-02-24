# #241 Ignore unused variables [Closed]

> Username: alandefreitas  
> Created at: 2022-07-07 19:55:21 UTC  
> Updated at: 2022-07-07 20:16:44 UTC  
> Closed at: 2022-07-07 20:10:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/241  



---

## Comment 1

> Username: Lastique  
> Created_at: 2022-07-07 20:10:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/241#issuecomment-1178169016  

Sorry, but no, I'm not willing to maintain workarounds like this. If the warnings are bothering you, please disable it with a pragma or a compiler option.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-07-07 20:16:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/241#issuecomment-1178175627  

Actually, it's even disabled [here](https://github.com/boostorg/filesystem/blob/fcc11010a5899d6a16d3f0a9d60704f88d62eada/include/boost/filesystem/detail/header.hpp#L39). It's just `header.hpp`/`footer.hpp` aren't included in the .cpp files.

---
