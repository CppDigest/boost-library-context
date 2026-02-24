# #19 Added support of reading dwarf debug symbols [Closed]

> Username: abelharisov  
> Created at: 2017-05-14 16:11:26 UTC  
> Updated at: 2020-12-06 18:19:24 UTC  
> Closed at: 2020-12-06 18:19:24 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19  

This adds support of mingw debug sybols. For using macro BOOST_STACKTRACE_USE_MGWHELP should be defined and binary should be linked with -lmgwhelp from [https://github.com/jrfonseca/drmingw](drmingw)

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-05-14 16:24:53 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#issuecomment-301323386  

[![Coverage Status](https://coveralls.io/builds/11513983/badge)](https://coveralls.io/builds/11513983)  
  
Coverage remained the same at 92.254% when pulling **12160d5993d261f33b785657d9628ec7dab15f38 on abelharisov:develop** into **bd52cde3b6512994f4e45448c47d7a393549a55d on boostorg:master**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-05-18 18:58:11 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#issuecomment-302510018  

Sorry, this won't work:  
Your pull request uses functions from DbgHelp library of Windows, which is a single threaded library (read as "utterly broken library"). Any attempt to workaround the single-threaded limitation by wrapping all the calls by mutexes are useless, as there's a lot of 3rd party code that uses DbgHelp and there's no way to wrap in mutexes their code in the Boost library.

---

## Comment 3

> Username: segevfiner  
> Created_at: 2017-07-13 20:45:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#issuecomment-315197041  

Wait... wasn't this for dwarf and mingw using mgwhelp... why is it using dbghelp at all...?

---

## Comment 4

> Username: apolukhin  
> Created_at: 2018-08-28 09:42:09 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#issuecomment-416519249  

Sorry for the delay. It took me some a year to understand this PR :)  
  
Please make the PR to the develop branch. I'll add some review comments soon.

---

## Review 5 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-08-29 10:43:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/19#pullrequestreview-150498749  

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
  19 |+ 
  20 |+     if( (getenv("_NT_SYMBOL_PATH") == NULL) && (getenv("_NT_ALTERNATE_SYMBOL_PATH") == NULL)) {
  21 |+         char local_app_data[MAX_PATH];
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:31:59 UTC  
> Updated_at: 2018-08-29 10:43:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213625473  

The body of the `if` sttement frightens me to death. It looks like it does http requests which is a very serious security threat. Is it possible to unconditionally skip the whole body and just call `SymInitialize` with `NULL`?

---

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
  10 |+ #include <boost/detail/winapi/get_current_process.hpp>
  11 |+ #include <windows.h>
  12 |+ #include <dbghelp.h>
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:35:23 UTC  
> Updated_at: 2018-08-29 10:43:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213626282  

I'd like to request two things here:  
* some link to the docs or sources of MinGW ensuring that MinGW's dbghelp is thread safe  
* some compile time check or `#error` to make sure that MinGW's dbghelp is used

---

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
  27 |+     }
  28 |+ 
  29 |+     return SymInitialize(process_handle, sym_search_path.c_str(), true);
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:38:20 UTC  
> Updated_at: 2018-08-29 10:43:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213626987  

Please prepend all the system calls and structures with `::` to disable ADL and make sure that the right function/class is used.

---

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
  34 |+     IMAGEHLP_LINE64 line;
  35 |+     memset(&line, 0, sizeof(IMAGEHLP_LINE64));
  36 |+     line.SizeOfStruct = sizeof(IMAGEHLP_LINE64);
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:39:11 UTC  
> Updated_at: 2018-08-29 10:43:49 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213627192  

`std::`

---

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
  81 |+         res += file_name;
  82 |+         res += ':';
  83 |+         res += boost::lexical_cast<boost::array<char, 40> >(line_number).data();
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:41:21 UTC  
> Updated_at: 2018-08-29 10:43:49 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213627737  

Upstream does not depend on `lexical_cast` any more. Please use the upstream approach for conversions.

---

📁 include/boost/stacktrace/detail/mgwhelp_impls.hpp

```diff
 100 |+     DWORD options = SymGetOptions();
 101 |+ 
 102 |+     if( SymFromAddr(process_handle, (DWORD64)addr, &displacement, symbol_info))
```

> Username: apolukhin  
> Created_at: 2018-08-29 10:42:54 UTC  
> Updated_at: 2018-08-29 10:43:49 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#discussion_r213628202  

Please, always use `{` and `}` for the `if` statements, even if the statement is single line.


---

## Comment 6

> Username: abelharisov  
> Created_at: 2020-12-06 18:19:24 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/19#issuecomment-739541843  

I close this pull request because my project, where I used this library, was abandoned several years ago.

---
