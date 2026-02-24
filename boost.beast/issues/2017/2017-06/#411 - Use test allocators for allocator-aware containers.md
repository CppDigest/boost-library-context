# #411 - Use test allocators for allocator-aware containers [Closed]

> Username: vinniefalco  
> Created at: 2017-06-01 20:13:09 UTC  
> Updated at: 2018-05-01 18:32:38 UTC  
> Closed at: 2018-05-01 18:32:38 UTC  
> Url: https://github.com/boostorg/beast/issues/411  

Maybe there's some in Boost, but @HowardHinnant has a set of allocators that stresses out containers when used in instantiations. We can use this to check Beast's allocator-aware containers.

---

## Comment 1

> Username: HowardHinnant  
> Created at: 2017-06-01 20:35:44 UTC  
> Url: https://github.com/boostorg/beast/issues/411#issuecomment-305612625  

They're all somewhere under here in the libc++ repository: https://github.com/llvm-mirror/libcxx/tree/master/test  The exercise the fancy pointer capabilities and everything else.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:41 UTC  
> Url: https://github.com/boostorg/beast/issues/411#issuecomment-384022656  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:29 UTC  
> Url: https://github.com/boostorg/beast/issues/411#issuecomment-385750189  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
