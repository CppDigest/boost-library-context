# #327 Fix issue with use of std::cout only in debug mode [Merged]

> Username: mkaravel  
> Created at: 2015-09-17 06:35:51 UTC  
> Updated at: 2015-10-14 08:14:56 UTC  
> Merged at: 2015-09-22 05:46:48 UTC  
> Closed at: 2015-09-22 05:46:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/327  

This is a follow up of PR #326 proposed by @jeremy-murphy.  
The problem is that when `debug_print_complement_graph()` is called, `std::cout` needs to be defined  
which requires the inclusion of `<iostream>` even in non-debug mode.  
  
With this PR the call to `debug_print_complement_graph()` is guarded by the appropriate  
macro and the use inclusion of `<iostream>` is no longer needed in non-debug mode.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2015-09-17 06:50:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-140985954  

If you're conditionally compiling the call of `debug_print_complement_graph()` then I think you don't need the non-debug definition of it in `debug_print_complement_graph.hpp` any more.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-09-17 06:53:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-140986336  

@jeremy-murphy I thought about that before making the changes and decided to leave it as it is; if `debug_print_complement_graph()` is to be used somewhere else (admittedly not very likely) it is safer to have the non-debug definition available as well.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2015-09-17 07:05:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-140988115  

Sure, no worries. Looks good! Thanks for acting on it so quickly.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-09-17 07:06:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-140988455  

@jeremy-murphy I will wait today in case somebody else wants to comment, and then merge tomorrow. Hope this is okay with you.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2015-09-21 23:57:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-142141774  

Still waiting for comment from others?

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2015-09-23 03:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-142482550  

Thanks, Menelaos.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-09-23 09:51:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/327#issuecomment-142548897  

@jeremy-murphy You are welcome. I am sorry for the delay.

---
