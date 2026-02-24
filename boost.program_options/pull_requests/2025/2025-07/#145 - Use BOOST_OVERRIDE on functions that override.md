# #145 Use BOOST_OVERRIDE on functions that override [Merged]

> Username: bmagistro  
> Created at: 2025-07-29 14:54:23 UTC  
> Updated at: 2025-09-12 15:21:56 UTC  
> Merged at: 2025-09-12 15:21:45 UTC  
> Closed at: 2025-09-12 15:21:45 UTC  
> Url: https://github.com/boostorg/program_options/pull/145  

When compiling with clang 19.1.7 on Alma with -Werror and a number of other flags (we run with most warnings enabled), a number of functions are flagged as overriding a function but not marked with override eg `error: 'parse' overrides a member function but is not marked 'override' [-Werror,-Wsuggest-override]`.  Based on older tickets, the `BOOST_OVERRIDE` macro was used to address this.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7  
  
Replaces: https://github.com/boostorg/program_options/pull/97  
While ^^ addressed other issues, to keep scope and review simple, the other issues are not addressed in here.  
  
Resolves: https://github.com/boostorg/program_options/issues/93

---

## Comment 1

> Username: vprus  
> Created_at: 2025-08-19 12:16:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/145#issuecomment-3200520311  

Thanks for the PR. Would you be willing to modify it to use 'override' directly, without any macros, since there's no point supporting C++03.

---

## Comment 2

> Username: bmagistro  
> Created_at: 2025-08-26 14:55:12 UTC  
> Url: https://github.com/boostorg/program_options/pull/145#issuecomment-3224523895  

will certainly take a look.  this approach was chosen by looking at how it was approached in this/other components

---

## Comment 3

> Username: vprus  
> Created_at: 2025-09-02 08:42:52 UTC  
> Url: https://github.com/boostorg/program_options/pull/145#issuecomment-3244382383  

Thanks for the update, it seems the tests all fail to compile in a single place where a different order of "override" and "cost" is needed.

---

## Comment 4

> Username: bmagistro  
> Created_at: 2025-09-12 13:40:51 UTC  
> Url: https://github.com/boostorg/program_options/pull/145#issuecomment-3285347062  

sorry for the delay and not catching that one.

---

## Comment 5

> Username: vprus  
> Created_at: 2025-09-12 15:21:56 UTC  
> Url: https://github.com/boostorg/program_options/pull/145#issuecomment-3285708083  

Thanks, merged!

---
