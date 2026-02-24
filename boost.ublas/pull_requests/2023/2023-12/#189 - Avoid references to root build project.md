# #189 Avoid references to root build project. [Closed]

> Username: grafikrobot  
> Created at: 2023-12-24 17:40:31 UTC  
> Updated at: 2024-07-28 13:18:09 UTC  
> Closed at: 2024-07-28 13:18:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/189  

In order to make the library transportable, specifically so we can support modular Boost distribution, we need to avoid encoding absolute paths to the monolithic Boost structure. This changes the config check references to be symbolic, as they are already configured to be found automatically.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-01-14 06:11:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/189#issuecomment-1890859793  

FYI.. https://lists.boost.org/Archives/boost/2024/01/255704.php

---
