# #2403 - non virtual destructor warnings [Closed]

> Username: Sil3ntStorm  
> Created at: 2022-04-06 10:06:52 UTC  
> Updated at: 2022-06-14 17:35:39 UTC  
> Closed at: 2022-06-14 17:35:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2403  

### Version of Beast  
  
BOOST_BEAST_VERSION 300 as well as 318  
  
### Steps necessary to reproduce the problem  
  
As soon as any of the error files are included (e.g. `boost/beast/http/error.hpp`, `boost/beast/core/error.hpp`, `beast/websocket/error.hpp`, `boost/beast/zlib/error.hpp`) by virtue of including the respective headers (e.g. websocket.hpp) you will get warnings about `detail::error_codes` having virtual functions but non-virtual destructors.  
  
There are probably other occurrences of this as well, which should be fixed as well.  
  
### All relevant compiler information  
  
clang8, clang11

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-06 16:10:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2403#issuecomment-1090449260  

Thank you for the note. Can you give an example of a minimal program and the command line to compile it that yields these warnings?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-04-06 17:58:18 UTC  
> Updated at: 2022-04-06 17:58:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2403#issuecomment-1090567613  

I just built everything locally with both gcc and clang11, "-Wall -Wextra -pedantic -Werror" options, and no warnings. Also no warnings of this kind [in CI for clang11](https://github.com/boostorg/beast/runs/5574826297?check_suite_focus=true).
