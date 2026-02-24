# #552 - Investigate untested str4 state in serializer [Closed]

> Username: grisumbras  
> Created at: 2021-04-28 12:23:01 UTC  
> Updated at: 2024-04-08 16:58:38 UTC  
> Closed at: 2024-04-08 16:58:37 UTC  
> Url: https://github.com/boostorg/json/issues/552  

See  
https://app.codecov.io/gh/boostorg/json/compare/551/tree/include/boost/json/impl/serializer.ipp#D1L220  
https://app.codecov.io/gh/boostorg/json/compare/551/tree/include/boost/json/impl/serializer.ipp#D1L339  
  
The state is either untested, because the condition that leads to it is very rare, in which case it still should be recreated in tests.  
Or this state is genuinely unreachable and should be removed.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-04-08 16:58:37 UTC  
> Url: https://github.com/boostorg/json/issues/552#issuecomment-2043239926  

Fixed in 04b26837fdcbc2bc99186c48d930120a4d4a161d
