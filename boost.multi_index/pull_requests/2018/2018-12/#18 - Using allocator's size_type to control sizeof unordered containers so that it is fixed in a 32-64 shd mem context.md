# #18 Using allocator's size_type to control sizeof unordered containers so that it is fixed in a 32/64 shd mem context [Closed]

> Username: CerosDev  
> Created at: 2018-12-03 21:05:25 UTC  
> Updated at: 2018-12-30 11:22:00 UTC  
> Closed at: 2018-12-30 11:22:00 UTC  
> Url: https://github.com/boostorg/multi_index/pull/18  

Modified type of member variable, will now use the allocator traits's size_type.  
This is useful when used in a 32/64bits Boost interprocess shared memory context. Using the previous std::size_t makes the container size different in both architecture, therefore impossible to store in a shared memory.  
  
In most case, this makes no difference because the default allocator's size_type is std::size_t

---

## Comment 1

> Username: joaquintides  
> Created_at: 2018-12-30 11:22:00 UTC  
> Url: https://github.com/boostorg/multi_index/pull/18#issuecomment-450554112  

Changes already done to the main repository as explained at https://github.com/boostorg/multi_index/issues/17#issuecomment-450554048

---
