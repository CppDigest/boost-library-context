# #135 Missing BOOST_NORETURN for user defined functions [Merged]

> Username: bolry  
> Created at: 2019-12-05 09:21:12 UTC  
> Updated at: 2019-12-09 12:44:46 UTC  
> Merged at: 2019-12-09 12:43:46 UTC  
> Closed at: 2019-12-09 12:43:46 UTC  
> Url: https://github.com/boostorg/container/pull/135  

BOOST_NORETURN is missing for the interface of the throwning methods when BOOST_CONTAINER_USER_DEFINED_THROW_CALLBACKS is defined. Without them you can get a `'noreturn' function does return` warning from g++ at `boost/container/static_vector.hpp:46:4`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-12-09 12:44:46 UTC  
> Url: https://github.com/boostorg/container/pull/135#issuecomment-563221877  

Many thanks for the pull request!

---
