# #670 X3: error_handler should not skip whitespaces [Merged]

> Username: Kojoley  
> Created at: 2021-04-25 18:02:13 UTC  
> Updated at: 2021-04-26 11:49:51 UTC  
> Merged at: 2021-04-26 11:49:49 UTC  
> Closed at: 2021-04-26 11:49:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/670  

A skipper does this already and Spirit usually does not roll back skipper work. When there is no skipper, skipping whitespaces is absolutely wrong thing to do. For example, an error points to an end of the word where an additional input is expected, but there are whitespaces after it.  
  
The `skip_whitespace`/`skip_non_whitespace` were removed since they are not used anymore, and because they are also could trigger UB on a particular input due to unsafe `std::isspace` usage.  
  
Fixes #546.

---

## Comment 1

> Username: djowel  
> Created_at: 2021-04-25 23:00:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/670#issuecomment-826403651  

Go for it!

---
