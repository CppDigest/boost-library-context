# #207 - Is it possible to replace default scheduler implementation with the custom one? [Closed]

> Username: Warchant  
> Created at: 2019-06-15 10:28:46 UTC  
> Updated at: 2019-06-17 06:10:11 UTC  
> Closed at: 2019-06-17 06:10:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/207  

Is it possible to replace [default scheduler](https://sourcegraph.com/github.com/boostorg/fiber@develop/-/blob/src/context.cpp#L78) implementation with the custom one somehow? Without forking this repo.

---

## Comment 1

> Username: olk  
> Created at: 2019-06-17 06:10:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/207#issuecomment-502547411  

no, it's not possible - you can customize the scheduling algorithms (round-robin, priority, work-stealing, work.sharing, ...) by your own.
