# #92 - asan detects stack-buffer-overflow when using try/catch [Open]

> Username: mgaunard  
> Created at: 2020-03-17 15:23:30 UTC  
> Updated at: 2020-03-22 21:02:32 UTC  
> Url: https://github.com/boostorg/phoenix/issues/92  

catch_all_gen is holding the "try" sub-expression by reference, which of course is not valid.  
Holding them by value and using base_type instead of that_type fixes the bug.  
  
while, do/while and switch also have this bug.  
Interestingly 'if' and 'for' do not.  
  
is_actor appears to also not be consistently specialized.

---

## Comment 1

> Username: djowel  
> Created at: 2020-03-18 06:51:29 UTC  
> Url: https://github.com/boostorg/phoenix/issues/92#issuecomment-600454188  

Will you do a PR for this?

---

## Comment 2

> Username: mgaunard  
> Created at: 2020-03-22 16:59:34 UTC  
> Url: https://github.com/boostorg/phoenix/issues/92#issuecomment-602239451  

https://github.com/boostorg/phoenix/pull/94

---

## Comment 3

> Username: djowel  
> Created at: 2020-03-22 21:02:32 UTC  
> Url: https://github.com/boostorg/phoenix/issues/92#issuecomment-602272541  

> #94  
  
Wonderful! Many thanks!
