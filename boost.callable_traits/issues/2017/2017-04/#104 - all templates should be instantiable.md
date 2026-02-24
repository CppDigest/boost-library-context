# #104 - [review] all templates should be instantiable [Closed]

> Username: badair  
> Created at: 2017-04-09 03:24:40 UTC  
> Updated at: 2017-04-14 17:55:00 UTC  
> Closed at: 2017-04-14 17:55:00 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/104  

Some templates are not instantiable (e.g. is_const_member_v in C++11), which is technically "ill formed, no diagnostic required"  
  
http://lists.boost.org/Archives/boost/2017/04/234173.php

---

## Comment 1

> Username: badair  
> Created at: 2017-04-14 17:55:00 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/104#issuecomment-294202175  

Fixed via 2ed1520c0dd5169c1d51e82e3963b03736e6d021
