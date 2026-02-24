# #353 - Use handler_alloc in composed ops [Closed]

> Username: vinniefalco  
> Created at: 2017-05-10 19:42:38 UTC  
> Updated at: 2017-06-21 02:39:55 UTC  
> Closed at: 2017-06-21 02:39:55 UTC  
> Url: https://github.com/boostorg/beast/issues/353  

When composed operations use `multi_buffer` or other dynamic buffers they should use `handler_alloc` as the allocator.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 02:39:55 UTC  
> Url: https://github.com/boostorg/beast/issues/353#issuecomment-309946513  

It seems all the composed operations are already using `handler_alloc` where possible.
