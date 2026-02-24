# #489 Fine-grained identification of MSVC CXX version in traits.hpp [Closed]

> Username: JamesJCode  
> Created at: 2021-05-30 12:03:58 UTC  
> Updated at: 2023-01-06 10:25:55 UTC  
> Closed at: 2023-01-06 10:25:55 UTC  
> Url: https://github.com/boostorg/hana/pull/489  

More fine-grained solution for #475 without poisoning downstream compile directives.  
  
Tested against Microsoft Visual Studio Community 2019 Version 16.9.6 with `CMAKE_CXX_STANDARD` set to `11`, `14`, `17`, and `20`.

---

## Comment 1

> Username: ldionne  
> Created_at: 2022-03-31 17:19:53 UTC  
> Url: https://github.com/boostorg/hana/pull/489#issuecomment-1084879180  

Sorry for not noticing this easy fix, I focus all my efforts on libc++ these days. Can you rebase onto `develop` and re-push? I'll merge this.

---
