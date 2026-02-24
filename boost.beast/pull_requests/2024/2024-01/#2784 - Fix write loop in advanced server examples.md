# #2784 Fix write loop in advanced server examples [Merged]

> Username: ashtum  
> Created at: 2024-01-01 16:37:08 UTC  
> Updated at: 2024-01-02 19:03:47 UTC  
> Merged at: 2024-01-02 17:58:58 UTC  
> Closed at: 2024-01-02 17:58:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2784  

Fixes #2739

---

## Review 1 [Commented]

> Username: fpelliccioni  
> Created_at: 2024-01-02 08:24:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2784#pullrequestreview-1800076049  

📁 example/advanced/server-flex-awaitable/advanced_server_flex_awaitable.cpp

```diff
  37 | #include <algorithm>
  38 | #include <cstdlib>
  39 |+ #include <queue>
```

> Username: fpelliccioni  
> Created_at: 2024-01-02 08:24:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2784#discussion_r1439232714  

NIT: alphabetical order or includes


---

## Review 2 [Commented]

> Username: fpelliccioni  
> Created_at: 2024-01-02 08:25:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2784#pullrequestreview-1800076262  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
  29 | #include <algorithm>
  30 | #include <cstdlib>
  31 |+ #include <queue>
```

> Username: fpelliccioni  
> Created_at: 2024-01-02 08:25:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2784#discussion_r1439232851  

NIT: alphabetical order or includes


---

## Comment 3

> Username: fpelliccioni  
> Created_at: 2024-01-02 19:03:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2784#issuecomment-1874427111  

🚀

---
