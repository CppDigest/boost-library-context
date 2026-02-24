# #77 Suppress unused variable warnings on node_count (clang 15) [Closed]

> Username: ecatmur  
> Created at: 2022-09-07 10:00:22 UTC  
> Updated at: 2022-12-10 23:30:10 UTC  
> Closed at: 2022-12-10 23:30:10 UTC  
> Url: https://github.com/boostorg/intrusive/pull/77  

Clang 15 is smart enough to realize that node_count is incremented but never read (if constant_time_size is false or asserts are disabled).  
  
Use boost::ignore_unused to suppress the warning.

---

## Comment 1

> Username: ecatmur  
> Created_at: 2022-09-07 11:25:32 UTC  
> Url: https://github.com/boostorg/intrusive/pull/77#issuecomment-1239263955  

CI failure is 0xC0000139, some kind of DSO error idk. Presumably not relevant to this change.

---

## Comment 2

> Username: hdu-sdlzx  
> Created_at: 2022-12-07 12:43:40 UTC  
> Url: https://github.com/boostorg/intrusive/pull/77#issuecomment-1340913172  

See this warning on fedora 37.  
@igaztanaga Can we run CI again and merge this PR?

---
