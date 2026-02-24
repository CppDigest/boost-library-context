# #181 - boost::container::small_vector is not compatible with std::span [Closed]

> Username: jmelas  
> Created at: 2021-03-21 06:50:02 UTC  
> Updated at: 2021-04-22 14:08:59 UTC  
> Closed at: 2021-04-22 14:08:58 UTC  
> Url: https://github.com/boostorg/container/issues/181  

It seems the iterator is missing the "contiguous" concept.  
Compiler: MSVC 16.9.2

---

## Comment 1

> Username: jmelas  
> Created at: 2021-03-24 10:35:57 UTC  
> Url: https://github.com/boostorg/container/issues/181#issuecomment-805702489  

This is similar to issue #150

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-04-22 14:08:58 UTC  
> Url: https://github.com/boostorg/container/issues/181#issuecomment-824875015  

Thanks for the report. After closing issue #150, small_vector is now span-compatible.
