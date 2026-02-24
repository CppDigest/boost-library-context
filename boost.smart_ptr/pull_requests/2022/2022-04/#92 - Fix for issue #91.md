# #92 Fix for issue #91 [Closed]

> Username: JoergRuedenauer  
> Created at: 2022-04-13 09:33:34 UTC  
> Updated at: 2022-05-19 19:24:40 UTC  
> Closed at: 2022-05-19 19:24:40 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/92  

Fixes issue #91 by comparing the stored pointers.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-05-19 19:24:40 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/92#issuecomment-1132114513  

This change breaks the entire test suite, which relies on the current semantics of `operator<`, so it's not acceptable in this form, even if we wanted to change `operator<`.

---
