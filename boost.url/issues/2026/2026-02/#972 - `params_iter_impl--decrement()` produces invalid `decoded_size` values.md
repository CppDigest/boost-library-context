# #972 - `params_iter_impl::decrement()` produces invalid `decoded_size` values [Closed]

> Username: vinniefalco  
> Created at: 2026-02-05 18:38:21 UTC  
> Updated at: 2026-02-20 23:41:31 UTC  
> Closed at: 2026-02-20 23:41:31 UTC  
> Url: https://github.com/boostorg/url/issues/972  

## Summary  
  
`params_iter_impl::decrement()` computes incorrect `dk` (decoded key  
size) and `dv` (decoded value size) when a query parameter's value  
contains literal (unencoded) `=` characters. This causes  
`make_pct_string_view_unsafe` to be called with a `decoded_size` that  
exceeds the encoded size, violating the invariant of `pct_string_view`  
and producing out-of-bounds reads during downstream decode/compare  
operations.  
  
**Affected file:** `src/detail/params_iter_impl.cpp`, function  
`decrement()`, lines 196-201.  
  
**Trigger condition:** Any query parameter whose value contains one or  
more literal `=` characters (e.g. `?a=b=c`, `?redirect=http://x.com?k=1`,  
`?data=x=y=z`), accessed via backward iteration or `find_last()`.  
  
## Data Model  
  
The fields of `params_iter_impl` are documented at the top of the file:  
  
```  
pos     offset from start   0 = '?'  
nk      size of key         with '?' or '&'  
nv      size of value       with '='  
dk      decoded key size    no '?' or '&'  
dv      decoded value size  no '='  
```  
  
In `dereference()` (line 217-222), the calls are:  
  
```cpp  
if(nv)  
    return {  
        make_pct_string_view_unsafe(  
            p, nk - 1, dk),        // key view  
        make_pct_string_view_unsafe(  
            p + nk, nv - 1, dv)};  // value view  
```  
  
The invariant of `pct_string_view` requires `decoded_size <= encoded_size`.  
  
## Root Cause  
  
`decrement()` scans right-to-left. When it encounters an `=` it assumes  
that is the key-value separator. If a second `=` is found further left  
(meaning the first one was actually inside the value), it re-adjusts the  
boundary at lines 196-201:  
  
```cpp  
if(*p == '=')  
{  
    // value  
    nv = p1 - p; // with '='  
    dv += dk;  
    dk = 0;  
}  
```  
  
The transfer `dv += dk; dk = 0;` is incorrect. At this point `dk`  
contains its initial value of `1` (the separator overhead for `?` or  
`&`). This overhead belongs to the key, not the value. Transferring it  
to `dv` inflates the value overhead and zeroes out the key overhead,  
corrupting both final decoded sizes.  
  
By contrast, `setup()` (forward iteration) scans left-to-right, finds  
the first `=` as the separator, and computes `dk`/`dv` correctly.  
  
## Proof: `?a=b=c`  
  
Buffer after `?`: `a=b=c` (key=`a`, value=`b=c`)  
  
### Forward (`setup()` from `pos=0`) -- correct  
  
| Step | Action | dk | dv |  
|------|--------|----|----|  
| init | dk=1, dv=0 | 1 | 0 |  
| scan key | `a`, then hit `=` at pos 1 | 1 | 0 |  
| nk = 1+(1-0) = 2 | dk = 2-1 = 1 | **1** | 0 |  
| scan value | `b`, `=`, `c`, hit end | 1 | 0 |  
| nv = 5-1 = 4 | dv = 4-0-1 = 3 | 1 | **3** |  
  
Result: nk=2, nv=4, **dk=1**, **dv=3**  
  
### Backward (`decrement()` from end) -- incorrect  
  
| Step | Action | dk | dv |  
|------|--------|----|----|  
| init | dk=1, dv=1 | 1 | 1 |  
| scan back: `c`, `=` at pos 3 | nv=2, break | 1 | 1 |  
| scan back: `b`, `=` at pos 1 | nv=4, **dv+=dk -> dv=2**, **dk=0** | 0 | 2 |  
| scan back: `a`, hit begin | nk=2, dk=2-0=**2**, dv=4-2=**2** | **2** | **2** |  
  
Result: nk=2, nv=4, **dk=2**, **dv=2**  
  
### Consequences in `dereference()`  
  
| | Forward (correct) | Backward (buggy) |  
|---|---|---|  
| Key call | `make_pct_string_view_unsafe(p, 1, 1)` | `make_pct_string_view_unsafe(p, 1, 2)` |  
| Value call | `make_pct_string_view_unsafe(p+2, 3, 3)` | `make_pct_string_view_unsafe(p+2, 3, 2)` |  
  
The key's `decoded_size` (2) exceeds its `encoded_size` (1), violating  
the `pct_string_view` invariant.  
  
## Out-of-Bounds Read Path  
  
The corrupted `pct_string_view` propagates through:  
  
1. `dereference()` or `key()` returns a `pct_string_view` with  
   `decoded_size() > size()`  
2. `operator*` on `pct_string_view` creates a `decode_view` where  
   `dn_` (decoded size) is larger than `n_` (encoded size)  
3. `decoded_strcmp()` in `impl/decode_view.hpp` uses `dn_` as the loop  
   bound:  
  
```cpp  
auto const n0 = s0.size();   // dn_ = 2 (wrong, should be 1)  
auto n = (std::min)(n0, n1);  
auto it0 = s0.begin();  
while (n--)  
{  
    const char c0 = *it0++;  // 2nd iteration: OOB read past "a"  
    ...  
}  
```  
  
The loop reads `dn_` decoded characters from the encoded buffer. When  
`dn_ > actual_decoded_count`, the iterator walks past the end of the  
encoded string.  
  
## Reachable Call Paths  
  
- **`params_encoded_base::iterator::operator--()`** followed by  
  `operator*()` -- any backward iteration over params with `=` in values  
- **`find_last()` / `find_last_impl()`** in both `params_base` and  
  `params_encoded_base` -- calls `it.decrement()` then `*it.key() == key`  
- **`params_base::iterator::operator--()`** followed by `operator*()`  
  -- same bug through the decoded params path  
  
## Additional Affected Examples  
  
The bug manifests for any value with literal `=`:  
  
| Query | Key | Value | dk (correct) | dk (buggy) |  
|-------|-----|-------|-------------|------------|  
| `?a=b=c` | `a` | `b=c` | 1 | 2 |  
| `?a=b=c=d` | `a` | `b=c=d` | 1 | 2 |  
| `?===` | `` | `==` | 0 | 1 |  
| `?a==b` | `a` | `=b` | 1 | 2 |  
| `?%61=x=y` | `%61` | `x=y` | 1 | 2 |  
  
Each additional `=` re-discovery in the second loop repeats the transfer  
and can accumulate further error if combined with percent-encoding  
between the `=` signs.  
  
## Proposed Fix  
  
The fix is to not transfer the separator overhead from `dk` to `dv`.  
When `dk` is transferred, only the percent-encoding overhead should  
move; the initial `1` for the separator must stay with the key.  
  
### Option A: Adjust the transfer (minimal change)  
  
In `decrement()`, lines 200-201, change:  
  
```cpp  
// BEFORE (buggy)  
dv += dk;  
dk = 0;  
```  
  
to:  
  
```cpp  
// AFTER (fixed)  
dv += dk - 1;  
dk = 1;  
```  
  
This keeps the separator overhead (`1`) with `dk` and only transfers the  
percent-encoding overhead to `dv`.  
  
### Option B: Track separator overhead separately  
  
Introduce a local variable to separate concerns:  
  
```cpp  
void  
params_iter_impl::  
decrement() noexcept  
{  
    BOOST_ASSERT(index > 0);  
    --index;  
    std::size_t pct_dk = 0; // percent-encoding overhead in key region  
    std::size_t pct_dv = 0; // percent-encoding overhead in value region  
    auto const begin = ref.begin();  
    BOOST_ASSERT(pos > 0);  
    auto p1 = begin + (pos - 1);  
    auto p = p1;  
    // find key or '='  
    for(;;)  
    {  
        if(p == begin)  
        {  
            // key only, no value  
            nk = 1 + p1 - p; // with '?'  
            dk = nk - 1 - pct_dv; // -1 for separator, pct_dv used as scratch  
            nv = 0;  
            dv = 0;  
            pos -= nk;  
            return;  
        }  
        else if(*--p == '&')  
        {  
            // key only, no value  
            nk = p1 - p; // with '&'  
            dk = nk - 1 - pct_dv;  
            nv = 0;  
            dv = 0;  
            pos -= nk;  
            return;  
        }  
        if(*p == '=')  
        {  
            nv = p1 - p; // with '='  
            break;  
        }  
        if(*p == '%')  
            pct_dv += 2;  
    }  
    // find key and value  
    for(;;)  
    {  
        if(p == begin)  
        {  
            nk = 1 + p1 - p - nv; // with '?'  
            dk = nk - 1 - pct_dk; // -1 for separator  
            dv = nv - 1 - pct_dv; // -1 for '='  
            pos -= nk + nv;  
            return;  
        }  
        if(*--p == '&')  
        {  
            nk = p1 - p - nv; // with '&'  
            dk = nk - 1 - pct_dk;  
            dv = nv - 1 - pct_dv;  
            pos -= nk + nv;  
            return;  
        }  
        if(*p == '=')  
        {  
            // boundary shifts: what we thought was key is actually value  
            nv = p1 - p;  
            pct_dv += pct_dk; // transfer only percent-encoding overhead  
            pct_dk = 0;  
        }  
        else if(*p == '%')  
        {  
            pct_dk += 2;  
        }  
    }  
}  
```  
  
**Option A** is the minimal fix and is correct for all cases. **Option  
B** is a clearer rewrite that makes the overhead accounting explicit and  
eliminates the dual-purpose use of `dk`/`dv` as both accumulators and  
final values.  
  
## Failing Tests  
  
The following tests should be added. They exercise both forward and  
reverse iteration via the existing `check()` helper, plus explicit  
`find_last()` and `decoded_size()` verification.  
  
### `params_encoded_base_test::testRange()` additions  
  
```cpp  
// Values containing literal '=' — exercises decrement() bug  
check( "?a=b=c",       { { "a", "b=c" } } );  
check( "?k=v=w&x=y",   { { "k", "v=w" }, { "x", "y" } } );  
check( "?x=y&k=v=w",   { { "x", "y" }, { "k", "v=w" } } );  
check( "?a=b=c=d",     { { "a", "b=c=d" } } );  
check( "?===",         { { "", "==" } } );  
check( "?a==b",        { { "a", "=b" } } );  
check( "?a=1&b=2=3&c=4", { { "a", "1" }, { "b", "2=3" }, { "c", "4" } } );  
```  
  
### `params_base_test::testRange()` additions  
  
```cpp  
// Values containing literal '=' — exercises decrement() bug  
check( "?a=b=c",       { { "a", "b=c" } } );  
check( "?k=v=w&x=y",   { { "k", "v=w" }, { "x", "y" } } );  
check( "?x=y&k=v=w",   { { "x", "y" }, { "k", "v=w" } } );  
check( "?a=b=c=d",     { { "a", "b=c=d" } } );  
check( "?===",         { { "", "==" } } );  
check( "?a==b",        { { "a", "=b" } } );  
check( "?a=1&b=2=3&c=4", { { "a", "1" }, { "b", "2=3" }, { "c", "4" } } );  
```  
  
### `params_encoded_base_test::testObservers()` additions for `find_last`  
  
```cpp  
// find_last with values containing '='  
{  
    url_view u("?a=b=c&d=e");  
    params_encoded_view p = u.encoded_params();  
    auto it = p.find_last("a");  
    BOOST_TEST_NE(it, p.end());  
    BOOST_TEST_EQ(it->key, "a");  
    BOOST_TEST_EQ(it->value, "b=c");  
}  
{  
    url_view u("?a=1&b=x=y&a=2=3");  
    params_encoded_view p = u.encoded_params();  
    auto it = p.find_last("a");  
    BOOST_TEST_NE(it, p.end());  
    BOOST_TEST_EQ(it->key, "a");  
    BOOST_TEST_EQ(it->value, "2=3");  
}  
```  
  
### Explicit `decoded_size` invariant test  
  
```cpp  
// decoded_size must agree between forward and backward iteration  
{  
    url_view u("?a=b=c");  
    params_encoded_view p = u.encoded_params();  
  
    // Forward: begin, dereference  
    auto fwd = *p.begin();  
  
    // Backward: end, decrement, dereference  
    auto bwd_it = p.end();  
    --bwd_it;  
    auto bwd = *bwd_it;  
  
    // Key: encoded "a" (size 1), decoded "a" (size 1)  
    BOOST_TEST_EQ(fwd.key, bwd.key);  
    BOOST_TEST_EQ(fwd.key.decoded_size(), bwd.key.decoded_size());  
    BOOST_TEST(bwd.key.decoded_size() <= bwd.key.size());  
  
    // Value: encoded "b=c" (size 3), decoded "b=c" (size 3)  
    BOOST_TEST_EQ(fwd.value, bwd.value);  
    BOOST_TEST_EQ(fwd.value.decoded_size(), bwd.value.decoded_size());  
    BOOST_TEST(bwd.value.decoded_size() <= bwd.value.size());  
}  
```  
  
### Expected failures without fix  
  
| Test | What fails |  
|------|-----------|  
| `check("?a=b=c", ...)` | Reverse `is_equal(*it0, *it1)` — key comparison reads past buffer (dk=2 vs encoded size 1); value comparison fails (dv=2, actual decoded size 3) |  
| `check("?===", ...)` | Reverse: empty key gets dk=1 but encoded size is 0 — decoded_size > size |  
| `find_last("a")` on `"?a=b=c&d=e"` | `*it.key() == "a"` triggers OOB read via `decoded_strcmp` |  
| `decoded_size` invariant | `bwd.key.decoded_size()` returns 2, forward returns 1; `bwd.key.decoded_size() <= bwd.key.size()` fails (2 > 1) |
