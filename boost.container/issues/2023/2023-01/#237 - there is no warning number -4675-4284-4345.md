# #237 - there is no warning number /4675/4284/4345 [Closed]

> Username: marakew  
> Created at: 2023-01-14 09:40:15 UTC  
> Updated at: 2023-02-13 08:06:45 UTC  
> Closed at: 2023-02-13 08:05:46 UTC  
> Url: https://github.com/boostorg/container/issues/237  

config_begin.hpp(25): warning C4619: #pragma warning: there is no warning number '4284'  
config_begin.hpp(28): warning C4619: #pragma warning: there is no warning number '4345'  
config_begin.hpp(42): warning C4619: #pragma warning: there is no warning number '4675'  
  
add check about BOOST_MSVC some warn seems removed by msvc

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-02-13 08:06:44 UTC  
> Url: https://github.com/boostorg/container/issues/237#issuecomment-1427508386  

Many thanks for the report!
