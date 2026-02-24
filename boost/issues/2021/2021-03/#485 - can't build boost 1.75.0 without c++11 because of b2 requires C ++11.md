# #485 - can't build boost 1.75.0 without c++11 because of b2 requires C ++11 [Open]

> Username: gycherish  
> Created at: 2021-03-03 08:42:50 UTC  
> Updated at: 2021-03-03 14:58:03 UTC  
> Url: https://github.com/boostorg/boost/issues/485  



---

## Comment 1

> Username: mclow  
> Created at: 2021-03-03 14:58:03 UTC  
> Url: https://github.com/boostorg/boost/issues/485#issuecomment-789774108  

Yes, you can. You just need C++11 support to build b2. Once that is done, you can build all the rest of boost with C++03. (Ok, most of boost; some of the libraries require C++11 or 14 or 17)
