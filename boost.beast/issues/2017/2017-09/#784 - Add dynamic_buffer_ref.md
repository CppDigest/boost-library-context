# #784 - Add dynamic_buffer_ref [Closed]

> Username: vinniefalco  
> Created at: 2017-09-16 15:51:08 UTC  
> Updated at: 2019-02-16 14:01:28 UTC  
> Closed at: 2019-02-16 14:01:28 UTC  
> Url: https://github.com/boostorg/beast/issues/784  

net-ts forces beast to add a movable DynamicBuffer wrapper so that beast's dynamic buffers can be used with asio functions like `read_until`. To be fully consistent, all beast functions would have to pass dynamic buffers as `DynamicBuffer&&`, and take ownership via `move`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:51 UTC  
> Url: https://github.com/boostorg/beast/issues/784#issuecomment-384003663  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-16 14:01:28 UTC  
> Url: https://github.com/boostorg/beast/issues/784#issuecomment-464349515  

This is in
