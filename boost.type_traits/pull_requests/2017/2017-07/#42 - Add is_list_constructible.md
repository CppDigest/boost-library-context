# #42 Add is_list_constructible [Merged]

> Username: pdimov  
> Created at: 2017-07-15 20:07:52 UTC  
> Updated at: 2017-07-16 19:59:56 UTC  
> Merged at: 2017-07-16 18:04:57 UTC  
> Closed at: 2017-07-16 18:04:57 UTC  
> Url: https://github.com/boostorg/type_traits/pull/42  

Like `is_constructible`, but for `T{args...}` instead of `T(args...)`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-07-16 01:50:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/42#issuecomment-315575586  

All green.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-07-16 19:53:52 UTC  
> Updated_at: 2017-07-16 19:59:56 UTC  
> Url: https://github.com/boostorg/type_traits/pull/42#issuecomment-315632771  

We'll have to regenerate the HTML documentation as well; I could do a PR if you like but my DocBook XSL is 1.78 and the version in the current files is 1.77 so this results in a million changed files.

---
