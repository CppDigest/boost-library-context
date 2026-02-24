# #1 - Still not in Boost release? [Closed]

> Username: bc-lee  
> Created at: 2017-07-21 01:21:26 UTC  
> Updated at: 2017-07-28 22:16:51 UTC  
> Closed at: 2017-07-28 22:16:51 UTC  
> Url: https://github.com/boostorg/contract/issues/1  

Hi, I found that your `boost-contract` library is suitable for contract-based programming in C++ and I heared your library passed boost code-review, which is cool.  
  
However, the code review was almost [5 years ago](http://www.boost.org/community/review_schedule.html).  
  
Are there any plans to release this libray to boost?

---

## Comment 1

> Username: lcaminiti  
> Created at: 2017-07-21 03:42:00 UTC  
> Updated at: 2017-07-21 15:35:56 UTC  
> Url: https://github.com/boostorg/contract/issues/1#issuecomment-316894170  

Not a commitment but... I'm planning to release this library within a year. Assuming the Boost community still wants it, I'll release it in Boost. Otherwise, I'll just release it on GitHub.  
  
In the meanwhile, you are welcome to use the library from GitHub. Master is stable. I also keep the develop branch pretty stable and it's got the latest and greatest API. The version of the library in the develop branch is pretty much final... all implemented and tested, just doing a final update to the docs now.  
  
Docs from master with examples on how to use the library (including postcondition old values and subcontracting, but the API in the develop branch changed a bit):  
https://lcaminiti.github.io/boost-contract/doc/html/index.html
