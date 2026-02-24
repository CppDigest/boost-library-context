# #77 - Crash in MSVC Debug mode caused by disabling of iterator debugging in vector_sparse.hpp [Open]

> Username: Plug11  
> Created at: 2020-01-24 12:06:20 UTC  
> Updated at: 2024-05-30 06:53:56 UTC  
> Url: https://github.com/boostorg/ublas/issues/77  

This was a tricky issue to find (from where I started)!  
  
At the following line in code: https://github.com/boostorg/ublas/blob/cf72d035f4f54c1fca3fa8882007a338c03b594b/include/boost/numeric/ublas/vector_sparse.hpp#L34 the MSVC iterator debugging is temporarily switched off because it was causing issues when the sparse matrix was comparing iterators. The iterators are from different containers and the iterator debugging complains about this.  
  
However this method of fixing causes real problems for the following reason.  
_ITERATOR_DEBUG_LEVEL 2 results in a different definition of the std iterator classes (with extra members to enable debugging when it is temporarily disabled in this file we end up with two incompatible definitions and the potential (a reality in my case) for unexplained crashes in code very far from this library.  
  
I have an alternative fix which uses the _Unwrapped() function (see https://devblogs.microsoft.com/cppblog/stl-features-and-fixes-in-vs-2017-15-8/) which allows access to an unchecked version of the iterators. I think this is the basis for a solution, but so far I have only fixed instances that were causing assertions in the unit tests (see below) and I don't know the code well enough to determine whether there are other cases (it looks like there could very well be). Also, I don't know coding guidelines well enough to know whether this should be refactored to avoid many #ifs in the code.  
  
Review of this and a proper fix, very much appreciated :-)  
  
Here is the basis of the suggested fix:  
  
in `vector_sparse.hpp` remove the following preprocessor code:  
```cpp  
#ifdef BOOST_MSVC  
#define _BACKUP_ITERATOR_DEBUG_LEVEL _ITERATOR_DEBUG_LEVEL  
#undef _ITERATOR_DEBUG_LEVEL  
#define _ITERATOR_DEBUG_LEVEL 0  
#endif  
```  
... and at end of the file, remove:  
```cpp  
#ifdef BOOST_MSVC  
#undef _ITERATOR_DEBUG_LEVEL  
#define _ITERATOR_DEBUG_LEVEL _BACKUP_ITERATOR_DEBUG_LEVEL  
#undef _BACKUP_ITERATOR_DEBUG_LEVEL  
#endif  
```  
in matrix_sparse.hpp make the following change to operator == () functions, as required (lines 2118, 2305 & 2517 fix the unit tests) :  
  
```cpp  
#if defined BOOST_MSVC &&  defined _ITERATOR_DEBUG_LEVEL && _ITERATOR_DEBUG_LEVEL >= 2  
                  return it_._Unwrapped() == it.it_._Unwrapped();  
#else  
                  return it_ == it.it_;  
#endif  
```

---

## Comment 1

> Username: poelmanc  
> Created at: 2020-08-12 18:48:47 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-673047527  

We just upgraded our compilers to the latest MSVC 2019 16.7 and started getting an error `_BACKUP_ITERATOR_DEBUG_LEVEL undeclared identifier` in the standard `<span>` header with:  
`#include <boost/numeric/ublas/vector_sparse.hpp>`  
`#include <span>`  
Applying the proposed patch fixed that compile error for us, though our code doesn't exercise all code paths in a DEBUG build so I can't guarantee that it's a complete fix or that it works on much older MSVC compilers.  
  
As an alternate workaround, simply swapping the order to  
`#include <span>`  
`#include <boost/numeric/ublas/vector_sparse.hpp>`  
also fixed the compile error. Of course in large software projects it's hard to guarantee include ordering, since various headers `#include` all kinds of other headers, so the issue may keep popping up.  
  
In short a fix that removes the `_BACKUP_ITERATOR_DEBUG_LEVEL` macro sounds ideal, but I fear the full fix may need to be a bit more complex to support both older and newer compilers.

---

## Comment 2

> Username: mhoemmen  
> Created at: 2020-09-23 05:24:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-697141255  

@bassoy Hi Cem! : - )  We can make a patch with the above fix if it's OK, and also adjust it a bit to account for older compilers.  I just might not have a way to test _much_ older compiler versions.

---

## Comment 3

> Username: bassoy  
> Created at: 2020-09-25 07:47:28 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-698779453  

@mhoemmen Hi Mark! Nice to hear from you! Right now we are in the process of switching from the old matrix/vector implementation to the tensor implementation which is why we became lazy to fix such issues. :-( So it would be super okay if you provide a fix.   
  
By the way, the new tensor type will be soon based on engine types (see pull request https://github.com/boostorg/ublas/pull/90) which we had discussed for the C++ linear algebra [proposal](http://open-std.org/JTC1/SC22/WG21/docs/papers/2020/p1385r6.pdf). We intend to have tensor templates only and specialize them for matrix and vector types. Hopefully we can also support different tensor formats (CP/TT/HT/etc.) and provide a basis for tensor decompositions.

---

## Comment 4

> Username: mhoemmen  
> Created at: 2020-09-28 04:16:46 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-699759930  

Hi Cem!  Thanks for the reply!  It's great to hear that you're working on uBLAS.  I'm excited to see all the linear algebra folks working together : - D  
  
@bassoy wrote:  
> Right now we are in the process of switching from the old matrix/vector implementation to the tensor implementation....  
  
How close are y'all to finishing that?  We have a temporary local work-around, but we're trying to come up with a reproducer to send to MS.  If you're planning on deleting that code pretty soon, we might just wait to adopt the new version.  Thanks!

---

## Comment 5

> Username: bassoy  
> Created at: 2020-09-29 06:31:23 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-700480852  

> How close are y'all to finishing that? We have a temporary local work-around, but we're trying to come up with a reproducer to send to MS. If you're planning on deleting that code pretty soon, we might just wait to adopt the new version. Thanks!  
  
I think it will take a month until the development branch gets stabilized and merged into the master with the new tensor proposal from @amitsingh19975.  
  
For backward compatibility we need to stick with the 'legacy' implementation for a while and having two versions of matrix and vector in the development branch. I hope that we can provide a stable new version of dense vector and  matrix by the beginning of next year. Replacing the 'legacy' implementation with a newer version might require the approval of the boost community as this is a major API change.

---

## Comment 6

> Username: GreenGary  
> Created at: 2024-05-30 06:53:55 UTC  
> Url: https://github.com/boostorg/ublas/issues/77#issuecomment-2138807724  

I checked [ublas/include/boost/numeric/ublas/vector_sparse.hpp](https://github.com/boostorg/ublas/blob/cf72d035f4f54c1fca3fa8882007a338c03b594b/include/boost/numeric/ublas/vector_sparse.hpp#L34) from boost 1.85 and found it unchanged. Any update on this topic?
