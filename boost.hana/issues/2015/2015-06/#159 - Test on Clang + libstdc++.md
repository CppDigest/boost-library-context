# #159 - [Travis] Test on Clang + libstdc++ [Closed]

> Username: ldionne  
> Created at: 2015-06-28 11:21:24 UTC  
> Updated at: 2016-06-11 19:49:03 UTC  
> Closed at: 2015-07-01 06:15:08 UTC  
> Url: https://github.com/boostorg/hana/issues/159  

Generalization of #136.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-29 22:31:38 UTC  
> Url: https://github.com/boostorg/hana/issues/159#issuecomment-116866283  

This is almost working, except for the benchmarks which explode because of libstdc++'s naive implementation of `std::tuple`.
