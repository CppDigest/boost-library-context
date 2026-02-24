# #376 Fix security issue #364 and non-keyword subgraph parsing [Merged]

> Username: sehe  
> Created at: 2024-05-03 03:38:12 UTC  
> Updated at: 2024-05-10 06:09:02 UTC  
> Merged at: 2024-05-10 06:09:02 UTC  
> Closed at: 2024-05-10 06:09:02 UTC  
> Url: https://github.com/boostorg/graph/pull/376  

(Note: this PR builds in #375 and #374)  
  
Three commits in this PR:  
  
 1. ad11d2e8 Fix non-keyword subgraph parsing  
 1. 82f623f7 test_subgraphs verifies (keyword) subgraphs parse  
 1. 95b80a93 max_subgraph_nesting_level in read_graphviz_new  
  
The last one actually fixes #364

---

## Comment 1

> Username: TheZ3ro  
> Created_at: 2024-05-03 17:19:07 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2093446638  

Can confirm this fixes #364 correctly  
  
![image](https://github.com/boostorg/graph/assets/686894/0f00aa44-508d-464f-b2f3-7258fd4f5e0d)

---

## Comment 2

> Username: sehe  
> Created_at: 2024-05-08 13:01:19 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2100526598  

Updated for review comments https://github.com/boostorg/graph/issues/364#issuecomment-2100506381

---

## Comment 3

> Username: sehe  
> Created_at: 2024-05-08 13:42:14 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2100609225  

Added `test_subgraph_nesting_limit` to pin-down the new behavior.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2024-05-09 05:03:28 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2101940518  

Do you need to rebase this? It has all the changes from the second PR. (Never mind the Drone failures, they're just a network glitch.)

---

## Comment 5

> Username: sehe  
> Created_at: 2024-05-09 11:33:29 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2102491057  

> Do you need to rebase this? It has all the changes from the second PR. (Never mind the Drone failures, they're just a network glitch.)  
  
Not necessarily. It's how dependent PRs work in Github (note that I started all PRs with the warning and also explicitly stated it before creating them). Therefore, the second PR already had the same dependency, and it worked "fine":  
  
![image](https://github.com/boostorg/graph/assets/324097/f0ea9689-3b85-4fc5-ba24-70359dd39327)  
  
Rebasing first might simplify the revision graph every so slightly (it's why I prefer linear-only history with ff-only merges). As a late thought: should I have edited my name into the authors for test/graphviz_test.cpp?

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2024-05-10 06:07:07 UTC  
> Url: https://github.com/boostorg/graph/pull/376#issuecomment-2103931543  

OK, I'm still a bit confused as to why it is showing all the changes from the second PR, which is merged, but I'll merge this and see what happens.

---
