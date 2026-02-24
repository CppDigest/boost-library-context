# #116 - -Wpedantic warning in develop branch [Closed]

> Username: HDembinski  
> Created at: 2019-02-13 22:30:51 UTC  
> Updated at: 2019-02-14 19:58:43 UTC  
> Closed at: 2019-02-14 11:55:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/116  

../../boost/multiprecision/detail/number_base.hpp:1558:24: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
 struct number_category<__int128> : public mpl::int_<number_kind_integer> {};

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-14 11:56:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/116#issuecomment-463600793  

Fixed in develop.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-14 19:58:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/116#issuecomment-463770911  

Thank you! :+1:
