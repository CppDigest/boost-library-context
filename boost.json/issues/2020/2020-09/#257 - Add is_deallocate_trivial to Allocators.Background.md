# #257 - Add is_deallocate_trivial to Allocators.Background [Closed]

> Username: vinniefalco  
> Created at: 2020-09-03 15:42:10 UTC  
> Updated at: 2022-05-25 12:04:37 UTC  
> Closed at: 2022-05-25 12:04:37 UTC  
> Url: https://github.com/boostorg/json/issues/257  

it is a distinguishing feature of boost.json

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-25 11:09:42 UTC  
> Url: https://github.com/boostorg/json/issues/257#issuecomment-1137108711  

I don't think that Allocators.Background is the place for this, because it describes standard allocators and PMR, rather then JSON's approach. Meanwhile, [Allocators.storage_ptr](https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/allocators/storage_ptr.html) does mention the trait. Do we need something more elaborate?
