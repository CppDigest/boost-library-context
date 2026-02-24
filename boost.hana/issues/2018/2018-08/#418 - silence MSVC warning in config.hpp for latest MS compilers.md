# #418 - silence MSVC warning in config.hpp for latest MS compilers [Closed]

> Username: tobias-loew  
> Created at: 2018-08-17 09:36:23 UTC  
> Updated at: 2018-08-29 16:37:11 UTC  
> Closed at: 2018-08-29 16:37:10 UTC  
> Url: https://github.com/boostorg/hana/issues/418  

the warning in line 23 in config.hpp could be silenced for latest MSVC compilers (e.g. there could be a test for the value of __cplusplus)

---

## Comment 1

> Username: ldionne  
> Created at: 2018-08-29 13:45:25 UTC  
> Url: https://github.com/boostorg/hana/issues/418#issuecomment-416959037  

Can you check whether this has been resolved since https://github.com/boostorg/hana/pull/419 was merged?

---

## Comment 2

> Username: tobias-loew  
> Created at: 2018-08-29 16:37:10 UTC  
> Url: https://github.com/boostorg/hana/issues/418#issuecomment-417019720  

Yes, #419 solves this. Thanks!
