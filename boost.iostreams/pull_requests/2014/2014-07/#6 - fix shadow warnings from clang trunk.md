# #6 fix shadow warnings from clang trunk [Merged]

> Username: jhunold  
> Created at: 2014-07-15 05:59:58 UTC  
> Updated at: 2016-12-03 23:45:25 UTC  
> Merged at: 2016-12-03 23:45:25 UTC  
> Closed at: 2016-12-03 23:45:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/6  

This patch renames some constructor parameters to avoid shadow warning.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2016-08-29 10:26:45 UTC  
> Url: https://github.com/boostorg/iostreams/pull/6#discussion_r76585473  

Is this really needed? You have not touched other `int c`, and I do not see a declaration of the `c` in the outer scope.

---
