# #153 - Commit 140b50e: windows_shared_memory::priv_open_or_create doesn't anymore set member m_name [Closed]

> Username: aholzinger  
> Created at: 2021-09-22 09:23:09 UTC  
> Updated at: 2022-03-07 12:59:58 UTC  
> Closed at: 2022-03-07 12:56:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/153  

After constructing a windows_shared_memory member m_name remains empty, while it should reflect the name given to the windows_shared_memory object in the c'tor.  
  
The issue was introduced with commit 140b50e. The line  
`m_name = filename ? filename : "";`  
was removed, but no other code to set m_name was added.

---

## Comment 1

> Username: yobeonline  
> Created at: 2022-02-28 11:16:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/153#issuecomment-1054150480  

I ran into this problem too.

---

## Comment 2

> Username: yobeonline  
> Created at: 2022-02-28 11:43:24 UTC  
> Updated at: 2022-02-28 11:43:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/153#issuecomment-1054172240  

The new implementation is actually broken because `get_name()` returns `m_name.getn()` regardless of the width of the string actually stored in `m_name`. As a result, If `m_name` was initialized with a `const wchar_t *` then `get_name()` would still return 0, even if `if (filename) m_name = filename;` was reintroduced in the code.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2022-03-07 12:56:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/153#issuecomment-1060657984  

This was fixed in #171.

---

## Comment 4

> Username: aholzinger  
> Created at: 2022-03-07 12:59:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/153#issuecomment-1060660816  

Good news, thank you!
