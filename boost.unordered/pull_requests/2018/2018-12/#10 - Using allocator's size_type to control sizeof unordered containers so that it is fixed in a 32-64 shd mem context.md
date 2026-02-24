# #10 Using allocator's size_type to control sizeof unordered containers so that it is fixed in a 32/64 shd mem context [Closed]

> Username: CerosDev  
> Created at: 2018-12-03 20:50:15 UTC  
> Updated at: 2021-11-15 17:54:53 UTC  
> Closed at: 2021-11-15 17:53:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/10  

Modified type of member variable, will now use the allocator traits's `size_type`.  
This is useful when used in a 32/64bits Boost interprocess shared memory context. Using the previous `std::size_t` makes the container size different in both architecture, therefore impossible to store in a shared memory.  
  
In most case, this makes no difference because the default allocator's `size_type` is `std::size_t`

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-15 17:52:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/10#issuecomment-969165259  

@CerosDev Would you mind rebasing your branch against develop and then pushing so that we can get the CI running again?

---

## Comment 2

> Username: CerosDev  
> Created_at: 2021-11-15 17:54:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/10#issuecomment-969167881  

I opened this 3 years ago. I will just close this.

---
