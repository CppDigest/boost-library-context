# #452 Add macro BOOST_MSSTL_VERSION [Merged]

> Username: pdimov  
> Created at: 2022-11-09 00:16:43 UTC  
> Updated at: 2022-11-20 18:36:41 UTC  
> Merged at: 2022-11-20 18:36:36 UTC  
> Closed at: 2022-11-20 18:36:36 UTC  
> Url: https://github.com/boostorg/config/pull/452  

... as [suggested in 2018](https://lists.boost.org/Archives/boost/2018/07/242452.php). (Better late than never I guess.)

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-11-16 18:34:12 UTC  
> Url: https://github.com/boostorg/config/pull/452#issuecomment-1317493286  

@pdimov can you add some docs as well please?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-11-16 20:26:48 UTC  
> Url: https://github.com/boostorg/config/pull/452#issuecomment-1317625811  

Added documentation.  
  
While doing so noticed that the same table contains `BOOST_NO_WREGEX`, documented to be defined in `boost/regex.hpp`. Is this some sort of a leftover? It seems out of place here.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-11-20 18:35:51 UTC  
> Url: https://github.com/boostorg/config/pull/452#issuecomment-1321209558  

>While doing so noticed that the same table contains BOOST_NO_WREGEX, documented to be defined in boost/regex.hpp. Is this some sort of a leftover? It seems out of place here.  
  
Definitely a leftover, will remove.

---
