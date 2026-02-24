# #41 - nowide::cout crashes in multithreaded [Closed]

> Username: oomek  
> Created at: 2018-12-19 20:27:24 UTC  
> Updated at: 2018-12-20 12:04:10 UTC  
> Closed at: 2018-12-20 11:42:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/41  

When I call nowide::cout from different threads it crashes. std::cout works fine.

---

## Comment 1

> Username: oomek  
> Created at: 2018-12-20 11:42:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/41#issuecomment-448969419  

reviewfixes branch seemed to fix that problem, so closing.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2018-12-20 12:01:44 UTC  
> Url: https://github.com/boostorg/nowide/issues/41#issuecomment-448975103  

Just FYI there is no thread safety guarantee on neither std::cout/cerr nor on std::nowide::cout/cerr

---

## Comment 3

> Username: oomek  
> Created at: 2018-12-20 12:04:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/41#issuecomment-448975704  

I'm aware of it, but std::cout just produce mangled output. Thanks for fixing it though.
