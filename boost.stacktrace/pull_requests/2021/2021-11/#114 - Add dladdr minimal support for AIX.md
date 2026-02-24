# #114 Add dladdr minimal support for AIX [Merged]

> Username: Helflym  
> Created at: 2021-11-04 12:16:45 UTC  
> Updated at: 2024-06-12 15:13:01 UTC  
> Merged at: 2024-06-12 15:13:01 UTC  
> Closed at: 2024-06-12 15:13:01 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/114  

AIX doesn't provide dladdr syscall. This patch implements a minimal  
version in order to be able to compile stacktrace.

---

## Review 1 [Commented]

> Username: apolukhin  
> Created_at: 2024-06-10 19:17:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/114#pullrequestreview-2108583506  

I've noticed a leak in code, so changed it to store the std::string in `Dl_info`.  
  
Not quite sure that the result compiles on AIX. Could you please give it a try and propose fixes if something is wrong?

---

## Comment 2

> Username: coveralls  
> Created_at: 2024-06-10 19:43:30 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/114#issuecomment-2159157259  

[![Coverage Status](https://coveralls.io/builds/67998381/badge)](https://coveralls.io/builds/67998381)  
  
coverage: 86.35% (-7.5%) from 93.865%  
when pulling **51807cda552723e2a849c8826c736cda9ddc6fc1 on Helflym:develop**  
into **3f3f9020fbec1fe36800aca52054eb159db6af2d on boostorg:develop**.

---

## Comment 3

> Username: Helflym  
> Created_at: 2024-06-11 12:01:24 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/114#issuecomment-2160584863  

Sadly, I won't be able to do so, I don't have access to an AIX server anymore. I propose to let the user behind #89 tests this MR. Otherwise, you can either close it or merge as is hoping it works (the code being solely AIX it would be safe for other targets)

---
