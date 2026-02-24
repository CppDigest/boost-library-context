# #604 X3: Corner cases in alternative parser with container attribute [Merged]

> Username: Kojoley  
> Created at: 2020-05-30 19:12:57 UTC  
> Updated at: 2020-05-31 11:44:38 UTC  
> Merged at: 2020-05-31 11:44:35 UTC  
> Closed at: 2020-05-31 11:44:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/604  

Alternative parser were appending empty values with attributeless parsers, while it should not append anything at all.  
A container parser of alternative parser of container parser with container of variant of container attribute was failing to compile.  
  
It would be cleaner to simply store every subparser wrapped inside `alternative` because even the general way to call subparsers is via `parse_alternative`, but it will hurt parser expression tree readability.  
  
Fixes #603  
Fixes #394

---

## Comment 1

> Username: djowel  
> Created_at: 2020-05-31 01:05:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/604#issuecomment-636405578  

It's always nice that you are adding tests on changes like this. Much appreciated! Thank you for taking care of the library.

---
