# #1301 - Race in timout service [Closed]

> Username: vinniefalco  
> Created at: 2018-11-14 11:40:20 UTC  
> Updated at: 2018-11-28 00:42:53 UTC  
> Closed at: 2018-11-28 00:42:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1301  

When the timer is started and stopped there could be a race, the operations should be posted to the strand

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-28 00:42:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1301#issuecomment-442274536  

This should be fixed in 193
