# #17 Fix invalid const atomic builtin accesses [Closed]

> Username: kraj  
> Created at: 2018-08-29 05:06:33 UTC  
> Updated at: 2018-08-29 17:23:28 UTC  
> Closed at: 2018-08-29 08:39:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/17  

Clang fixed a signature issue with builtin atomic functions wrongly  
allowing const-qualified arguments. This change removes the invalid  
const qualifiers from those calls.  
  
Fixes:  
boost/atomic/detail/ops_gcc_x86_dcas.hpp:408:16: error: address argument  
to atomic builtin cannot be const-qualified ('const volatile  
boost::atomics::detail::gcc_dcas_x86_64::storage_type *' (aka 'const  
volatile unsigned __int128 *') invalid)  
        return __sync_val_compare_and_swap(&storage, value, value);  
  
Signed-off-by: Khem Raj <raj.khem@gmail.com>

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-29 08:39:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/17#issuecomment-416873299  

There is no `__sync_val_compare_and_swap` in the current implementation, this was fixed in https://github.com/boostorg/atomic/commit/6e14ca24dab50ad4c1fa8c27c7dd6f1cb791b534.

---

## Comment 2

> Username: kraj  
> Created_at: 2018-08-29 17:23:24 UTC  
> Url: https://github.com/boostorg/atomic/pull/17#issuecomment-417035459  

seems to fix the issue, thanks for info.

---
