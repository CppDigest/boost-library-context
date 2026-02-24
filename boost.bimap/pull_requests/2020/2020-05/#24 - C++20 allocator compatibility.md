# #24 C++20 allocator compatibility [Closed]

> Username: mgaunard  
> Created at: 2020-05-14 12:30:13 UTC  
> Updated at: 2020-05-21 15:18:21 UTC  
> Closed at: 2020-05-21 15:18:20 UTC  
> Url: https://github.com/boostorg/bimap/pull/24  

Fixes #23   
  
Since C++20, rebind has been removed from allocator, the replacement is to use allocator_traits which is only available since C++11.  
  
For compatibility with C++03 I've opted to use boost container allocator_traits instead.

---

## Comment 1

> Username: mrks  
> Created_at: 2020-05-19 13:34:36 UTC  
> Url: https://github.com/boostorg/bimap/pull/24#issuecomment-630822380  

Duplicate of #15?

---

## Comment 2

> Username: mgaunard  
> Created_at: 2020-05-19 22:45:48 UTC  
> Url: https://github.com/boostorg/bimap/pull/24#issuecomment-631126581  

Subset of it yes, didn't see #15 because allocator<void> is another issue.

---

## Comment 3

> Username: glenfe  
> Created_at: 2020-05-21 14:27:19 UTC  
> Url: https://github.com/boostorg/bimap/pull/24#issuecomment-632116906  

I don't think Bimap depends directly on Container right now. Instead we have the following in Core: https://www.boost.org/doc/libs/master/libs/core/doc/html/core/allocator_access.html  
  
Instead of `typename boost::container::allocator_traits<A>::template portable_rebind_alloc<U>::type` can you please use `typename boost::allocator_rebind<A, U>::type`.

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-05-21 15:18:20 UTC  
> Url: https://github.com/boostorg/bimap/pull/24#issuecomment-632144550  

I've addressed this in 6fba6e5de3a5d9b0675a875fdac8aad9238d6dd8 .

---
