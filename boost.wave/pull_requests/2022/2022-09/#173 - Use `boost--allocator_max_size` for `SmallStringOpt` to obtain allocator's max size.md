# #173 Use `boost::allocator_max_size` for `SmallStringOpt` to obtain allocator's max size [Merged]

> Username: frederick-vs-ja  
> Created at: 2022-09-05 03:28:54 UTC  
> Updated at: 2022-09-22 13:07:48 UTC  
> Merged at: 2022-09-13 22:08:14 UTC  
> Closed at: 2022-09-13 22:08:14 UTC  
> Url: https://github.com/boostorg/wave/pull/173  

Since direct call to `a.max_size()` may fail to compile since C++11.  
  
I found the fix was missing for `SmallStringOpt` when building a project at work in C++20 mode (where `std::allocator`'s `max_size` is removed).  
  
Related to #85, #86, #93.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2022-09-05 06:46:30 UTC  
> Url: https://github.com/boostorg/wave/pull/173#issuecomment-1236601560  

Wave requires C++11 since 1.79 - maybe we can just use `std::allocator_traits<>` ?

---

## Comment 2

> Username: frederick-vs-ja  
> Created_at: 2022-09-05 09:25:19 UTC  
> Url: https://github.com/boostorg/wave/pull/173#issuecomment-1236754848  

> Wave requires C++11 since 1.79 - maybe we can just use `std::allocator_traits<>` ?  
  
I think this is OK. But doing so would partially revert the changes in #93, and might violate @glenfe's intent.

---

## Comment 3

> Username: jefftrull  
> Created_at: 2022-09-13 20:53:17 UTC  
> Url: https://github.com/boostorg/wave/pull/173#issuecomment-1245943367  

Yes, I think you are right. There is little practical benefit to switching to `std::allocator_traits` and a bit of work. Making this update maintains consistency with the existing code, as well.

---

## Comment 4

> Username: hkaiser  
> Created_at: 2022-09-22 13:07:47 UTC  
> Url: https://github.com/boostorg/wave/pull/173#issuecomment-1254998838  

@jefftrull Thanks a lot!

---
