# #449 simplified coverage options and added docs [Merged]

> Username: HDembinski  
> Created at: 2019-06-08 20:45:09 UTC  
> Updated at: 2021-10-02 21:14:02 UTC  
> Merged at: 2019-06-09 14:03:10 UTC  
> Closed at: 2019-06-09 14:03:10 UTC  
> Url: https://github.com/boostorg/build/pull/449  

Thank you for working on the coverage feature. I discovered the previous work by accident. This patch simplifies the options to just "on" and "off". `-fprofile-arcs` could be used alone, but there is no use case known to me for instrumenting the code but then not write the results somehwere. `-ftest-coverage` doesn't work without `-fprofile-arcs`. I argue the options should be kept simple, unless more fine-grained control is actually requested, and then more options can be added on top of "on" and "off".  
  
This patch also adds documentation.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2019-06-08 22:22:49 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/449#pullrequestreview-247383682  

📁 src/tools/features/coverage-feature.jam

```diff
  10 |+     :
  11 |+     on          # Enable coverage generation for the tool.
  12 |+     off         # Disable coverage generation for the tool.
```

> Username: grafikrobot  
> Created_at: 2019-06-08 22:19:26 UTC  
> Updated_at: 2019-06-09 12:11:55 UTC  
> Url: https://github.com/boostorg/build/pull/449#discussion_r291821197  

`off` needs to be the default by placing it first on the values.

> Username: HDembinski  
> Created_at: 2019-06-09 12:12:32 UTC  
> Url: https://github.com/boostorg/build/pull/449#discussion_r291835574  

Right... fixed in the new patch.


---
