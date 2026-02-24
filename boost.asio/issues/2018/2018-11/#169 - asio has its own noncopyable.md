# #169 - asio has its own noncopyable? [Closed]

> Username: jeking3  
> Created at: 2018-11-20 14:06:05 UTC  
> Updated at: 2020-12-30 00:57:28 UTC  
> Closed at: 2020-12-30 00:57:27 UTC  
> Url: https://github.com/boostorg/asio/issues/169  

Just something I noticed that might be redundant:  
```  
boost@a538fe233607:/boost/libs/thread$ find .. -name noncopyable.hpp  
../core/include/boost/core/noncopyable.hpp  
../core/include/boost/noncopyable.hpp  
../asio/include/boost/asio/detail/noncopyable.hpp  
```

---

## Comment 1

> Username: viccpp  
> Created at: 2018-11-22 09:56:33 UTC  
> Url: https://github.com/boostorg/asio/issues/169#issuecomment-440975313  

Yes, ASIO has its own noncopyable

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-11 15:43:04 UTC  
> Url: https://github.com/boostorg/asio/issues/169#issuecomment-446248656  

Boost.Asio comes from standalone-Asio thats why

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:57:26 UTC  
> Url: https://github.com/boostorg/asio/issues/169#issuecomment-752290665  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#654](https://github.com/chriskohlhoff/asio/issues/654).
