# #109 fixed definition of list [Merged]

> Username: joaquintides  
> Created at: 2025-10-26 11:02:06 UTC  
> Updated at: 2025-10-30 08:46:50 UTC  
> Merged at: 2025-10-30 08:46:49 UTC  
> Closed at: 2025-10-30 08:46:50 UTC  
> Url: https://github.com/boostorg/mp11/pull/109  

A list is not a class template (e.g. `mp_list`) but an instantation of it (e.g. `mp_list<int>`).

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-26 14:34:59 UTC  
> Url: https://github.com/boostorg/mp11/pull/109#issuecomment-3448593690  

It doesn't say "class template" though. :-)

---

## Comment 2

> Username: joaquintides  
> Created_at: 2025-10-26 18:28:57 UTC  
> Url: https://github.com/boostorg/mp11/pull/109#issuecomment-3448755444  

If "template class" is to be taken as meaning "instantiation of a class template", the definition does not make sense, as instantiations of class templates, being types, can't be variadic or have parameters.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-26 18:34:53 UTC  
> Url: https://github.com/boostorg/mp11/pull/109#issuecomment-3448761691  

That's what it used to mean in the standard, although I see that it's no longer used in the latest draft.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-10-26 18:48:02 UTC  
> Url: https://github.com/boostorg/mp11/pull/109#issuecomment-3448770788  

> as instantiations of class templates, being types, can't be variadic or have parameters.  
  
Technically correct, they can only have arguments, not parameters.  
  
Your fix is still wrong though, because it needs to change "template class" into "class template" in order to make sense.

---
