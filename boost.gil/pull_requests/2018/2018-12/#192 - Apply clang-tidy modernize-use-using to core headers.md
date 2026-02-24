# #192 Apply clang-tidy modernize-use-using to core headers [Merged]

> Username: mloskot  
> Created at: 2018-12-15 03:48:58 UTC  
> Updated at: 2018-12-16 02:05:59 UTC  
> Merged at: 2018-12-15 18:10:31 UTC  
> Closed at: 2018-12-15 18:10:31 UTC  
> Url: https://github.com/boostorg/gil/pull/192  

Run [clang-tidy 7.0](http://clang.llvm.org/extra/clang-tidy/) with `-checks='-*,modernize-use-using' -fix` against single TU with `#include <boost/gil/*.hpp>`.  
  
-----  
  
The `modernize-use-using` check is not bulletproof, works 50/50, so manual review and modifications have been applied.  
  
The 1-3 headers modernization per commit is applied for easier review in chunks.  
  
### References  
  
- [[cfe-users] [run-clang-tidy] new replacement overlaps with an existing replacement](http://lists.llvm.org/pipermail/cfe-users/2018-December/001450.html)  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-15 18:04:58 UTC  
> Url: https://github.com/boostorg/gil/pull/192#issuecomment-447586791  

@stefanseefeld Thanks! Obviously, I'm going to squash and merge this PR as single commit. I assume you agree, correct?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-12-15 18:06:28 UTC  
> Url: https://github.com/boostorg/gil/pull/192#issuecomment-447586889  

On Sat, Dec 15, 2018, 13:05 Mateusz Loskot <notifications@github.com wrote:  
  
> @stefanseefeld <https://github.com/stefanseefeld> Thanks! Obviously, I'm  
> going to squash and merge this PR as single commit. I assume you agree,  
> correct?  
>  
Correct. :-)

---
