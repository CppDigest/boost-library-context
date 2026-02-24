# #238 - Segfault with ucontext on macOS (#184, continued) [Open]

> Username: francoisk  
> Created at: 2023-09-06 09:20:46 UTC  
> Updated at: 2024-03-10 19:27:32 UTC  
> Url: https://github.com/boostorg/context/issues/238  

Although #213, accepted in #184, did apparently fix part of the problem, Boost.Context's own example programs still segfault due to the `_XOPEN_SOURCE` issue described in that pull request: if a Boost.Context client includes (for example) a standard C++ header before a Boost.Context one, `_XOPEN_SOURCE` is not defined when the `ucontext` struct is defined, resulting in its definition having fewer fields than when `_XOPEN_SOURCE` is defined, as Boost.Context expects it to be. Thus the segfault.  
  
For example, I fixed the *fibonacci* example's segfault by moving `#include <boost/context/fiber.hpp>` above the inclusions of the stdlib headers.  
  
Seems like the best workaround at this point is to export `-D_XOPEN_SOURCE` to clients from Boost.Context's build.

---

## Comment 1

> Username: olk  
> Created at: 2023-10-01 14:41:54 UTC  
> Updated at: 2023-10-01 14:42:27 UTC  
> Url: https://github.com/boostorg/context/issues/238#issuecomment-1742102664  

Could you check if this issue is solved by #236 (branch develop)?! I can't do it because I don't own a MAC system.

---

## Comment 2

> Username: francoisk  
> Created at: 2024-01-03 08:52:45 UTC  
> Url: https://github.com/boostorg/context/issues/238#issuecomment-1875025883  

Finally got to it but looks like the problem persists. I tested develop at `be271d3e4758b260947b9b0c20855c1ba33cf346`. The fibonacci example still segfaults and moving the `fiber.hpp` include to the top still fixes it.

---

## Comment 3

> Username: olk  
> Created at: 2024-03-10 19:27:32 UTC  
> Url: https://github.com/boostorg/context/issues/238#issuecomment-1987338562  

seams to be an issue of defining _XOPEN_SOURCE in fiber.hpp
