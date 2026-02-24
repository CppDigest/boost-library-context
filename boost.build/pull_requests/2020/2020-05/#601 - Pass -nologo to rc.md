# #601 Pass /nologo to rc [Merged]

> Username: Kojoley  
> Created at: 2020-05-10 17:38:04 UTC  
> Updated at: 2021-10-02 21:02:02 UTC  
> Merged at: 2020-05-11 00:55:25 UTC  
> Closed at: 2020-05-11 00:55:25 UTC  
> Url: https://github.com/boostorg/build/pull/601  

Supported at least with Resource Compiler Version 6.1.7600.16385 that comes in Windows SDK v7.0A, the oldest you could obtain nowadays.

---

## Review 1 [Commented]

> Username: grafikrobot  
> Created_at: 2020-05-11 00:38:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/601#pullrequestreview-408800189  

📁 src/tools/clang-win.jam

```diff
 176 |         toolset.flags clang-win.link .MT $(cond) : $(manifest-tool) -nologo ;
 177 |         toolset.flags clang-win.compile .MC $(cond) : $(mc-compiler) ;
 178 |-         toolset.flags clang-win.compile .RC $(cond) : $(resource-compiler) /nologo ;
```

> Username: grafikrobot  
> Created_at: 2020-05-11 00:38:21 UTC  
> Url: https://github.com/boostorg/build/pull/601#discussion_r422725307  

I'm confused.. Is this not already adding `/nologo` to the RC command? It should.. If it's not it's likely a bug.

> Username: Kojoley  
> Created_at: 2020-05-11 00:41:07 UTC  
> Url: https://github.com/boostorg/build/pull/601#discussion_r422725635  

This is Clang and it adds /nologo, but MSVC and Intel-win does not, so I removed it from Clang and added globally instead.

> Username: grafikrobot  
> Created_at: 2020-05-11 00:55:05 UTC  
> Url: https://github.com/boostorg/build/pull/601#discussion_r422727716  

Oh, haha.. I missed that.


---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:01 UTC  
> Url: https://github.com/boostorg/build/pull/601#issuecomment-932820014  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
