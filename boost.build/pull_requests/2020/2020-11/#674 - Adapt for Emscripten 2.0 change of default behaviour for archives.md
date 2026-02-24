# #674 Adapt for Emscripten 2.0 change of default behaviour for archives [Merged]

> Username: bfierz  
> Created at: 2020-11-24 21:48:26 UTC  
> Updated at: 2021-10-02 21:01:26 UTC  
> Merged at: 2020-11-24 23:04:11 UTC  
> Closed at: 2020-11-24 23:04:11 UTC  
> Url: https://github.com/boostorg/build/pull/674  

When building archives Emscripten 2.0 requires the usage of the `-r` in order to create an archive and not link an executable. According to the build logs, the parameter was optional in 1.x and the behavior was inferred from the file ending.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:25 UTC  
> Url: https://github.com/boostorg/build/pull/674#issuecomment-932819914  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
