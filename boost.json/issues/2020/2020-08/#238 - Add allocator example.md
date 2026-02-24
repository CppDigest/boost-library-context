# #238 - Add allocator example [Closed]

> Username: vinniefalco  
> Created at: 2020-08-31 18:28:36 UTC  
> Updated at: 2022-05-25 12:05:39 UTC  
> Closed at: 2022-05-25 12:05:39 UTC  
> Url: https://github.com/boostorg/json/issues/238  

We could use an example program which shows how to write an Allocator that wraps a `storage_ptr`, to be used wherever an allocator is expected.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-25 11:22:40 UTC  
> Url: https://github.com/boostorg/json/issues/238#issuecomment-1137119757  

Wraps in what way? The defining feature of `storage_ptr` is that it can own a memory resource. But allocators are supposed to be cheap to copy handles to memory resources, they shouldn't own anything. Or are we talking about uses_allocator construction?
