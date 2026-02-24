# #57 Change the linkage of a variable in the header [Closed]

> Username: d7d1cd  
> Created at: 2025-10-21 14:46:54 UTC  
> Updated at: 2025-10-22 16:29:00 UTC  
> Closed at: 2025-10-22 12:16:54 UTC  
> Url: https://github.com/boostorg/describe/pull/57  

The `modifiers` constant can be declared as `constexpr` or `const `. In either case, its linkage becomes `static`, which leads to a compile-time error ('exposes TU-local') when using any entity that depends on `modifiers` in a C++20 module.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-21 18:44:01 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3428793972  

Inline variables are C++17. I'll take care of it.

---

## Comment 2

> Username: d7d1cd  
> Created_at: 2025-10-22 05:12:30 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3430526672  

@pdimov, maybe it makes sense to add inline only when the code is compiled using the C++20 standard and higher?

---

## Comment 3

> Username: d7d1cd  
> Created_at: 2025-10-22 15:56:58 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3433129080  

Why is PR closed?  
@pdimov

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-10-22 16:11:27 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3433181043  

https://github.com/boostorg/describe/commit/373db784111d6f5d2376b9e7ec36f7d207be290a

---

## Comment 5

> Username: d7d1cd  
> Created_at: 2025-10-22 16:26:56 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3433234398  

I'm sorry, but I don't understand the context of this change. And who will make it?  
@pdimov

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-10-22 16:29:00 UTC  
> Url: https://github.com/boostorg/describe/pull/57#issuecomment-3433240904  

I don't understand what you're asking. I already made the change, and it will be in the next release.

---
