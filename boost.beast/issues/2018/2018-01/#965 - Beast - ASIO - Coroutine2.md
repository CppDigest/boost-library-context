# #965 - Beast / ASIO / Coroutine2 [Closed]

> Username: rberlich  
> Created at: 2018-01-03 10:42:35 UTC  
> Updated at: 2018-01-03 13:49:28 UTC  
> Closed at: 2018-01-03 13:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/965  

Hi there,  
  
from what I can see, ASIO Coroutine handling (and hence Beast coroutine handling) is not based on Coroutine 2, and its predecessor has been marked deprecated.   
  
There is not much information to be found regarding if and when ASIO will move to Coroutine 2. As this will have an impact on Beast, do you have any insight in how far this will change the API of ASIO and Beast ?  
  
Thanks and a Happy New Year,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-03 13:15:20 UTC  
> Url: https://github.com/boostorg/beast/issues/965#issuecomment-355009149  

There should be no impact. Programs which use stackful coroutines will not have to change. The deprecation warning will eventually be dealt with in a manner that is transparent.

---

## Comment 2

> Username: rberlich  
> Created at: 2018-01-03 13:49:28 UTC  
> Url: https://github.com/boostorg/beast/issues/965#issuecomment-355015875  

Thanks a lot!
