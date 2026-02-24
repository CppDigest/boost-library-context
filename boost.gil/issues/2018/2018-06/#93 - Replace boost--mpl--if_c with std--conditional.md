# #93 - Replace boost::mpl::if_c with std::conditional [Closed]

> Username: mloskot  
> Created at: 2018-06-17 21:25:04 UTC  
> Updated at: 2019-04-14 20:22:42 UTC  
> Closed at: 2019-04-14 20:22:41 UTC  
> Url: https://github.com/boostorg/gil/issues/93  

There are `~20` uses of `boost::mpl::if_c` which can be replaced with [std::conditional](http://en.cppreference.com/w/cpp/types/conditional) as direct equivalent.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-04-14 20:22:41 UTC  
> Url: https://github.com/boostorg/gil/issues/93#issuecomment-483055083  

Closed by #274
