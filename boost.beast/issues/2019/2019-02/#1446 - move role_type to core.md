# #1446 - move role_type to core/ [Closed]

> Username: vinniefalco  
> Created at: 2019-02-10 06:11:25 UTC  
> Updated at: 2019-03-12 13:16:40 UTC  
> Closed at: 2019-03-12 13:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1446  

It is annoying to have to include `websocket/role.hpp` just to implement teardown, this could be moved to core. And an alias placed in websocket namespace for compatbility. We might consider making teardown a core interface. We might also change the websocket implementation to call `get_lowest_layer` on the next layer to obtain the interface upon which to perform teardown. Then, intermediate layers don't have to all forward the teardown hooks.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-03-12 07:03:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1446#issuecomment-471881343  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-12 13:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1446#issuecomment-471995736  

Done
