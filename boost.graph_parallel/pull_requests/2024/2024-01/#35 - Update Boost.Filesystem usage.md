# #35 Update Boost.Filesystem usage [Merged]

> Username: Lastique  
> Created at: 2024-01-26 22:23:09 UTC  
> Updated at: 2024-01-31 08:55:40 UTC  
> Merged at: 2024-01-31 02:42:47 UTC  
> Closed at: 2024-01-31 02:42:47 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/35  

Removed usage of Boost.Filesystem APIs that were deprecated and then removed.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-01-31 01:51:56 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/35#issuecomment-1918223783  

Thanks for doing this, but do you know why all the checks failed?

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-01-31 01:58:14 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/35#issuecomment-1918230409  

The CI config is outdated. Some images are no longer provided by GitHub Actions (see the the annotations [here](https://github.com/boostorg/graph_parallel/actions/runs/7674057284)). For those images that do still exist, C++03 tests have failed because dependent libraries have dropped C++03. I think, you can safely remove C++03 from the CI config at this point.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-01-31 02:42:40 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/35#issuecomment-1918268673  

OK, something else for the to-do list.

---
