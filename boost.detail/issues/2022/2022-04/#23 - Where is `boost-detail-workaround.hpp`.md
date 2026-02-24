# #23 - Where is `boost/detail/workaround.hpp`? [Closed]

> Username: prodrigs  
> Created at: 2022-04-11 11:41:02 UTC  
> Updated at: 2022-04-11 12:38:25 UTC  
> Closed at: 2022-04-11 12:38:24 UTC  
> Url: https://github.com/boostorg/detail/issues/23  

A number of files in `boost/type_traits` include the header `boost/detail/workaround.hpp`. However, there is no such file here and compilation breaks if `boost/detail` is included as a git submodule to some project. Curiously, the needed header file (and a few more) are available in boost-1.78.0.tar.gz and compilation goes smoothly if boost is compiled and installed first as an external library. Aren't these modules synchronised with the official releases?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-04-11 12:38:24 UTC  
> Url: https://github.com/boostorg/detail/issues/23#issuecomment-1094999011  

It's in [Boost.Config](https://github.com/boostorg/config/blob/0fb824a2745cae82ddfaf587a7ea44b58252f08a/include/boost/detail/workaround.hpp). The header is deprecated and forwards to `boost/config/workaround.hpp`.
