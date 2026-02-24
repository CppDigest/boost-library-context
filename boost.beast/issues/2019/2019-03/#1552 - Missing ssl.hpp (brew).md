# #1552 - Missing ssl.hpp (brew) [Closed]

> Username: benstadin  
> Created at: 2019-03-31 01:07:38 UTC  
> Updated at: 2019-03-31 12:53:39 UTC  
> Closed at: 2019-03-31 12:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1552  

I've installed Boost 1.69.0 on OS X with brew, but it's missing <boost/beast/ssl.hpp>. Is it possible to install it separately, or should I rather build boost from sources?

---

## Comment 1

> Username: djarek  
> Created at: 2019-03-31 01:08:51 UTC  
> Updated at: 2019-03-31 01:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1552#issuecomment-478302604  

`boost/beast/ssl.hpp` has been added in 1.70. Try installing from source.

---

## Comment 2

> Username: benstadin  
> Created at: 2019-03-31 11:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1552#issuecomment-478333268  

Thanks for the explanation, this issue can be closed then.
