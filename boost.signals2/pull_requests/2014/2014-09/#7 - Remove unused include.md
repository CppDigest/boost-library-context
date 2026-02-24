# #7 Remove unused include [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-26 09:23:40 UTC  
> Updated at: 2014-09-30 06:10:16 UTC  
> Merged at: 2014-09-26 22:27:06 UTC  
> Closed at: 2014-09-26 22:27:06 UTC  
> Url: https://github.com/boostorg/signals2/pull/7  

This header file does not compile with clang-cl because of the try/catch, and it's not used anyway since 44f2fc27d825482456f75673921d11cd71f904e4.

---
