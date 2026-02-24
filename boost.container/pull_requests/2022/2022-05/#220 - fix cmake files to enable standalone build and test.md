# #220 fix cmake files to enable standalone build and test [Closed]

> Username: ClausKlein  
> Created at: 2022-05-29 20:44:38 UTC  
> Updated at: 2022-10-25 03:30:11 UTC  
> Closed at: 2022-10-25 03:30:11 UTC  
> Url: https://github.com/boostorg/container/pull/220  

prevent name conflict with `bcopy` macro found in `strings.h` on some  
unix systems, i.e. **android**

---

## Comment 1

> Username: ClausKlein  
> Created_at: 2022-05-29 20:53:42 UTC  
> Url: https://github.com/boostorg/container/pull/220#issuecomment-1140522623  

@igaztanaga I dit not understand how to build with the distributed `CMakeLists.txt`. And the `test/CMakeLists.txt` was missing?

---
