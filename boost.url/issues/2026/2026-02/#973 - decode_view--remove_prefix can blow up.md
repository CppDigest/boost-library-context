# #973 - decode_view::remove_prefix can blow up [Closed]

> Username: vinniefalco  
> Created at: 2026-02-05 20:57:31 UTC  
> Updated at: 2026-02-20 23:41:31 UTC  
> Closed at: 2026-02-20 23:41:31 UTC  
> Url: https://github.com/boostorg/url/issues/973  

## `remove_prefix` can blow up if `n > size()`  
  
Here is the implementation:  
  
```52:65:c:\Users\Vinnie\src\boost\libs\url\src\decode_view.cpp  
void  
decode_view::  
remove_prefix( size_type n )  
{  
    auto it = begin();  
    auto n0 = n;  
    while (n)  
    {  
        ++it;  
        --n;  
    }  
    n_ -= (it.base() - begin().base());  
    dn_ -= n0;  
    p_ = it.base();  
}  
```  
  
### Step 1: Understanding the data members  
  
From the header, `decode_view` has:  
- `p_` — pointer to the encoded character buffer  
- `n_` — size of the **encoded** string (bytes)  
- `dn_` — the **decoded** size (logical character count, returned by `size()`)  
  
### Step 2: Understanding `operator++()`  
  
The iterator's `operator++` reads `*pos_` to decide whether to advance by 1 or 3:  
  
```70:78:c:\Users\Vinnie\src\boost\libs\url\include\boost\url\impl\decode_view.hpp  
    iterator&  
    operator++() noexcept  
    {  
        BOOST_ASSERT(pos_ != nullptr);  
        if (*pos_ != '%')  
            ++pos_;  
        else  
            pos_ += 3;  
        return *this;  
    }  
```  
  
This is the critical point: **every `++it` dereferences the current position to decide how far to advance.**  
  
### Step 3: Tracing the failure  
  
Consider encoded string `"A%20B"` — decoded `"A B"`, so `n_` = 5, `dn_` = 3.  
  
Call `remove_prefix(4)` (one more than `size()`):  
  
| Iteration | n | `*pos_` | advance | pos_ after |  
|---|---|---|---|---|  
| 1 | 4→3 | `'A'` | +1 | `p_+1` |  
| 2 | 3→2 | `'%'` | +3 | `p_+4` |  
| 3 | 2→1 | `'B'` | +1 | `p_+5` (one-past-end) |  
| 4 | 1→0 | **`*(p_+5)`** | ? | ? |  
  
Iteration 4 reads `*pos_` at `p_+5`, which is **past the end of the buffer**. This is an out-of-bounds read — hard undefined behavior. Depending on what garbage byte it reads, it might advance by 1 or by 3, pushing the pointer even further into invalid memory.  
  
### Step 4: The arithmetic also goes wrong  
  
Even if the OOB read doesn't crash immediately, the aftermath is also broken:  
  
- `dn_ -= n0`: with `dn_` = 3 and `n0` = 4, this is unsigned underflow → wraps to `std::size_t(-1)`, an enormous value.  
- `n_ -= (it.base() - begin().base())`: if the iterator overshot past `n_` bytes, this also underflows.  
  
The resulting `decode_view` object would have a corrupt, nonsensical state.  
  
### Step 5: The documented precondition is too weak  
  
The javadoc says:  
  
```462:464:c:\Users\Vinnie\src\boost\libs\url\include\boost\url\decode_view.hpp  
        @par Preconditions  
        @code  
        not this->empty()  
```  
  
This only guards against calling it on an empty view. It does **not** guard against `n > size()`. By contrast, `std::string_view::remove_prefix` documents the precondition as `n <= size()`.  
  
### Step 6: No debug assertion either  
  
Unlike `front()` and `back()` which have `BOOST_ASSERT( !empty() )`, the `remove_prefix` body has **zero assertions**. There's nothing to catch this in debug builds.  
  
### Summary  
  
There are two concrete problems:  
  
1. **UB via out-of-bounds read**: When `n > dn_`, the `while(n)` loop calls `++it` past the end of the buffer, and `operator++` dereferences `*pos_` to decide the advancement distance — reading invalid memory.  
  
2. **Unsigned integer underflow**: `dn_ -= n0` wraps around, leaving the view in a corrupt state with a near-maximum `size()`.  
  
The fix would be to either add `BOOST_ASSERT(n <= dn_)` at the top of the function (matching what `std::string_view` requires), or to clamp `n` to `dn_`. The documentation should also be updated to state `n <= size()` as the precondition instead of `not this->empty()`.
