# #169 Split single boost/gil/concepts.hpp into boost/gil/concepts/*.hpp [Merged]

> Username: mloskot  
> Created at: 2018-11-17 21:19:07 UTC  
> Updated at: 2018-11-17 22:36:56 UTC  
> Merged at: 2018-11-17 22:36:30 UTC  
> Closed at: 2018-11-17 22:36:30 UTC  
> Url: https://github.com/boostorg/gil/pull/169  

Replaces single, big, complex, of unclear logical structure, difficult to reason about `concepts.hpp` with collection of structured, clearly named headers.  
  
This PR consists of _pure refactoring_ changes:  
  
- move definitions to new files  
- replace Doxygen comments `/** and `**/` with simple `///` all the way  
- format some extremely long lines and template definitions to readable form respecting80-90 characters long lines  
- move some auxiliary internal definitions into `namespace detail`  
- add `#include` for missing headers  
- add `#pragma GCC diagnostic ignored "-Wunused-local-typedefs"`  
  
This PR does NOT:  
- introduce functional changes  
- modify content of comments  
- modify tests  
  
### References  
  
- [[rfc] Splitting concepts.hpp](https://lists.boost.org/boost-gil/2018/10/0110.php) thread on boost-gil ML  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-11-17 21:35:53 UTC  
> Url: https://github.com/boostorg/gil/pull/169#issuecomment-439649333  

Thanks for this work ! I find it quite hard to review, as the diff mostly just display added vs. deleted content, rather than providing insight into how things were re-arranged.  
I assume this is (mostly) just about moving chunks of code into different locations, then using include directives to re-assemble them as needed ?  
In other words, there aren't any semantically relevant changes to the code itself, right ?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-11-17 21:44:34 UTC  
> Updated_at: 2018-11-17 21:54:03 UTC  
> Url: https://github.com/boostorg/gil/pull/169#issuecomment-439649784  

@stefanseefeld Yes, it is difficult to review indeed.  
  
> I assume this is (mostly) just about moving chunks of code into different locations,  
> then using include directives to re-assemble them as needed ?  
  
Correct.  
  
> In other words, there aren't any semantically relevant changes to the code itself, right ?  
  
No, no functional changes introduced. Pure refactoring (as labelled :-))  
  
I have just updated description of this PR listing what kind of changes it introduces.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-11-17 22:11:23 UTC  
> Url: https://github.com/boostorg/gil/pull/169#issuecomment-439651262  

OK, thanks for confirming that. So I think all looks very good !

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-11-17 22:36:19 UTC  
> Url: https://github.com/boostorg/gil/pull/169#issuecomment-439652601  

Thanks

---
