# #1566 - websocket::stream move ctor is not great [Closed]

> Username: vinniefalco  
> Created at: 2019-04-11 01:01:23 UTC  
> Updated at: 2019-04-19 01:24:14 UTC  
> Closed at: 2019-04-19 01:24:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1566  

After a move construction, the moved-from object can only be destroyed

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 01:24:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1566#issuecomment-484738902  

I just looked it over, I think this contract is fine the way it is. The alternative is a move-constructor that can fail, which is worse.
