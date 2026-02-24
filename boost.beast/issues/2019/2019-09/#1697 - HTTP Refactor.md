# #1697 - HTTP Refactor [Closed]

> Username: vinniefalco  
> Created at: 2019-09-09 17:25:58 UTC  
> Updated at: 2022-06-16 16:37:45 UTC  
> Closed at: 2022-06-16 16:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1697  

This is a master discussion thread for thoughts and ideas related to the upcoming HTTP refactor  
  
---  
  
`basic_parser` is cheaply copyable  
  
We might make `parser` cheaply copyable  
  
We could require Body Reader and Body Writer to be cheaply copyable. Some implementations might be forced to use `shared_ptr`.  
  
Alternatively we could require _MoveConstructible_ (?)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-10-09 17:39:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1697#issuecomment-540109087  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1697#issuecomment-1157891327  

HTTP is being redesigned in https://github.com/CPPAlliance/http_proto
