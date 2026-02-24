# #32 - Optimization: channel long reads [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:56:19 UTC  
> Updated at: 2022-12-10 12:12:10 UTC  
> Closed at: 2022-12-10 12:12:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/32  

Currently, channel performs 2 read operations per packet. Add some buffering strategy, as Boost.Beast does, so we can perform fewer read calls.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-12-10 12:12:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/32#issuecomment-1345251704  

Done as of bf4071f37062bd180a495fc73241becb32aa2c6e.
