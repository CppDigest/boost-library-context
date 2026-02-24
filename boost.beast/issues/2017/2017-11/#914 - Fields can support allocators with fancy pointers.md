# #914 - Fields can support allocators with fancy pointers [Closed]

> Username: glenfe  
> Created at: 2017-11-28 13:57:00 UTC  
> Updated at: 2018-04-28 21:08:41 UTC  
> Closed at: 2018-04-28 21:08:41 UTC  
> Url: https://github.com/boostorg/beast/issues/914  

Instead of:  
> `static_assert(std::is_pointer<typename std::allocator_traits<Allocator>::pointer>::value, "Allocator must use regular pointers");`  
  
You could:  
> `using allocator_type = typename std::conditional<std::is_pointer<typename std::allocator_traits<Allocator>::pointer>::value, Allocator, boost::alignment::aligned_allocator_adaptor<Allocator> >::type;`  
  
Then all those allocators with fancy pointers (like `offset_ptr`) should just work.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-28 14:13:02 UTC  
> Url: https://github.com/boostorg/beast/issues/914#issuecomment-347535234  

This needs to be a pull request. And to be legit, it also needs to be added to the unit test or else how will we know if it works? And I'm not sure I want to add the dependency on Boost.Align.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/914#issuecomment-384003599  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-28 21:08:41 UTC  
> Url: https://github.com/boostorg/beast/issues/914#issuecomment-385205536  

When a user complains then we'll implement it
