# #1832 - moved-from objects should not be cleared if allocator unequal [Closed]

> Username: vinniefalco  
> Created at: 2020-01-30 21:14:39 UTC  
> Updated at: 2020-02-05 14:40:52 UTC  
> Closed at: 2020-02-05 14:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1832  

dynamic buffers like `flat_buffer` should not be clever and clear the moved-from container when the allocator compares unequal.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-03 10:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1832#issuecomment-581342543  

This is a question. There may be use cases I'm not aware of.  
  
I'm interested to understand the design rationale behind providing:  
  
`flat_buffer(flat_buffer&&, Allocator const& alloc)` at all.  
  
This can only perform a move if the allocators compare equal, in which case it seems to me that a caller would have nothing to gain by supplying the allocator and an r-value.  
  
I am wondering if we'd lose anything by only providing:  
  
`flat_buffer(flat_buffer const&, Allocator const& alloc)`  
`flat_buffer(flat_buffer const&)`  
`flat_buffer(flat_buffer &&) noexcept`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-02-03 14:57:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1832#issuecomment-581454103  

It comes up in generic contexts. All std containers have allocator-extended move constructors.
