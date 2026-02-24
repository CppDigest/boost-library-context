# #34 Fix compilation in c++1z [Merged]

> Username: blastrock  
> Created at: 2016-10-02 16:33:01 UTC  
> Updated at: 2016-10-02 17:42:17 UTC  
> Merged at: 2016-10-02 17:42:17 UTC  
> Closed at: 2016-10-02 17:42:17 UTC  
> Url: https://github.com/boostorg/context/pull/34  

invoke() could resolve to both boost::context::detail::invoke and std::invoke  
because of ADL, which would result in an ambiguous call error  
  
This patch was tested on debian unstable, with gcc 6.2 and clang 3.9. Also, I didn't test the patch in this repository but directly on boost 1.61, but it should be safe enough.

---

## Comment 1

> Username: olk  
> Created_at: 2016-10-02 17:41:45 UTC  
> Url: https://github.com/boostorg/context/pull/34#issuecomment-250984001  

thx - I'll add defect macro for boost.config regrading to std::invoke() soon

---
