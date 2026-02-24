# #57 - windows_error.hpp deprecation warning [Closed]

> Username: johnwason  
> Created at: 2020-12-30 01:36:07 UTC  
> Updated at: 2021-01-02 20:17:31 UTC  
> Closed at: 2021-01-02 20:17:31 UTC  
> Url: https://github.com/boostorg/system/issues/57  

I saw the deprecation warning during a build when using `windows_error.hpp`. I am still using the Windows error codes for interacting with LibUSB and other low-level functions. I can work around it but it would be easier to not have this removed. I suspect a lot of other people are using it for some niche purposes.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-01-02 14:21:39 UTC  
> Url: https://github.com/boostorg/system/issues/57#issuecomment-753479243  

Thanks for the feedback! I will remove the deprecation notice from `windows_error.hpp`. Out of curiosity, is your code using it publicly available?

---

## Comment 2

> Username: johnwason  
> Created at: 2021-01-02 19:51:51 UTC  
> Url: https://github.com/boostorg/system/issues/57#issuecomment-753521777  

No, I don't have anything open source using it right now.
