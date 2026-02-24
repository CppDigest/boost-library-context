# #683 Adding top-level index.html which redirects to the doc/html/index.htm… [Closed]

> Username: eldiener  
> Created at: 2020-12-13 00:56:35 UTC  
> Updated at: 2021-10-02 21:01:19 UTC  
> Closed at: 2020-12-17 15:41:40 UTC  
> Url: https://github.com/boostorg/build/pull/683  

…l, where the documentation gets built by default. A Boost release expects a top-level index.html brings up the documentation for a library or a tool.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-12-14 17:16:55 UTC  
> Url: https://github.com/boostorg/build/pull/683#issuecomment-744585182  

Which access is this trying to resolve? Is it only for the Boost release tarball? Something else?

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-12-14 17:48:58 UTC  
> Updated_at: 2020-12-14 17:49:36 UTC  
> Url: https://github.com/boostorg/build/pull/683#issuecomment-744603601  

For any given Boost release the documentation link expects an 'index.html' in a library or tool's top-level directory. Open, for example, the Boost 1.75 documentation in your browser as distributed by the 'index.html' of that distribution in the Boost top-level directory. Now click 'Tools' and click the Boost.Build link. You'll get a page not found because the Boost documentation expects an 'index.html' in the 'tools/build' subdirectory. My PR adds that file, which redirects to the actual Boost.Buil;d documentation in the distribution.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2020-12-17 15:41:40 UTC  
> Url: https://github.com/boostorg/build/pull/683#issuecomment-747518576  

Resolved in a different way.. https://github.com/boostorg/build/commit/64a895a3314e152bef58cc912c1e375a3d202f79

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:18 UTC  
> Url: https://github.com/boostorg/build/pull/683#issuecomment-932819896  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
