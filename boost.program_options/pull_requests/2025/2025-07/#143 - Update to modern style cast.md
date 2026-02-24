# #143 Update to modern style cast [Merged]

> Username: bmagistro  
> Created at: 2025-07-29 03:04:06 UTC  
> Updated at: 2025-08-19 12:09:23 UTC  
> Merged at: 2025-08-19 12:09:23 UTC  
> Closed at: 2025-08-19 12:09:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/143  

When compiling with clang 19.1.7 on Alma with -Werror and a number of other flags (we run with most warnings enabled), the lines were flagged with `error: use of old-style cast [-Werror, -Wold-style-cast]`. It is unclear if this was just missed or cast style was not updated.  Additionally, `0` was switched to `nullptr` for improved clarity in these lines.  
  
The updates here were found in conjunction with https://github.com/boostorg/function/pull/55.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7

---

## Comment 1

> Username: vprus  
> Created_at: 2025-08-19 12:09:15 UTC  
> Url: https://github.com/boostorg/program_options/pull/143#issuecomment-3200494910  

Thanks for the PR.   
  
Tehnically, the use of nullptr is a C++11 feature, but apparently shared_ptr now requires a C++11 compiler, so program_options would not build with C++03 anyway.

---
