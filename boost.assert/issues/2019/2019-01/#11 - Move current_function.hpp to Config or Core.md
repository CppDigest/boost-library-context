# #11 - Move current_function.hpp to Config or Core [Closed]

> Username: mloskot  
> Created at: 2019-01-12 14:19:24 UTC  
> Updated at: 2019-01-12 16:13:11 UTC  
> Closed at: 2019-01-12 16:13:10 UTC  
> Url: https://github.com/boostorg/assert/issues/11  

Would it be reasonable and possible to move `current_function.hpp` to Boost.Config or Boost.Core  
as a bit more general purpose feature than just for assertion macros? /cc @pdimov

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-12 15:48:29 UTC  
> Url: https://github.com/boostorg/assert/issues/11#issuecomment-453757683  

Assert is a lower level dependency than Core (Core depends on Assert rather than vice versa), so no.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-01-12 16:13:10 UTC  
> Url: https://github.com/boostorg/assert/issues/11#issuecomment-453759574  

Got it. Thanks for the explanation.
