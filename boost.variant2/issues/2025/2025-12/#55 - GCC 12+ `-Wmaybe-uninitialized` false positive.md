# #55 - GCC 12+ `-Wmaybe-uninitialized` false positive [Closed]

> Username: vinniefalco  
> Created at: 2025-12-31 13:48:40 UTC  
> Updated at: 2026-01-02 14:01:11 UTC  
> Closed at: 2026-01-02 14:01:11 UTC  
> Url: https://github.com/boostorg/variant2/issues/55  

(Disclaimer: AI-generated analysis)  
  
## Issue: GCC 12+ `-Wmaybe-uninitialized` false positive with `variant2` containing non-trivially-copyable types  
  
### Summary  
  
GCC 12 and later emit spurious `-Wmaybe-uninitialized` warnings when copying or move-assigning a `boost::variant2::variant` that contains types with non-trivial copy/move constructors (e.g., `std::exception_ptr`, `std::string`).  
  
### Affected GCC Versions  
- GCC 12.x ✓  
- GCC 13.x ✓  
- GCC 14.x ✓  
- GCC 15.x ✓  
  
### Not Affected  
- Clang (all versions)  
- MSVC (all versions)  
- GCC 11 and earlier  
  
### Trigger Conditions  
1. `variant<T, U>` where T or U has a **non-trivial copy or move constructor** that accesses members  
2. Compiled with **`-O3`** (aggressive inlining exposes variant internals)  
3. Compiled with **`-Werror=maybe-uninitialized`**  
4. The variant is copied or move-assigned  
  
### Minimal Reproducer  
  
```cpp  
// mrtc.cpp  
#include <boost/variant2/variant.hpp>  
#include <exception>  
  
namespace v2 = boost::variant2;  
  
void handler(v2::variant<v2::monostate, std::exception_ptr>) {}  
  
int main() {  
    v2::variant<v2::monostate, std::exception_ptr> v;  // holds monostate (index 0)  
    handler(v);  // copy triggers warning  
}  
```  
  
**Compile with GCC 12+:**  
```bash  
g++-12 -std=c++20 -O3 -Wall -Wextra -Werror mrtc.cpp -I /path/to/boost  
```  
  
**Expected:** Compiles cleanly    
**Actual:**   
```  
error: '...exception_ptr::_M_exception_object' may be used uninitialized [-Werror=maybe-uninitialized]  
```  
  
### Root Cause  
  
When copying a variant, the copy constructor contains code paths for all alternatives via `mp_with_index`. GCC 12+'s improved `-Wmaybe-uninitialized` analysis sees the code path that would copy the `exception_ptr` (index 1) and warns that `_M_exception_object` may be uninitialized—even though the variant's discriminator guarantees that path won't execute when index is 0.  
  
GCC's dataflow analysis cannot prove the discriminator ensures only initialized alternatives are accessed.  
  
### Also Reproduces With  
  
- `variant<std::string, std::exception_ptr>` — warns about `string::_M_string_length`  
- `boost::system::result<void, std::exception_ptr>` (uses variant2 internally)  
- Any variant where an alternative has a non-trivial copy/move that accesses members  
  
### Does NOT Reproduce With  
  
- `variant<int, error_code>` — `error_code` uses implicit byte-copy, no member access  
- Types with trivial copy/move constructors  
  
### Suggested Fix  
  
Add GCC-specific pragmas around the copy/move constructors and assignment operators:  
  
```cpp  
#if defined(__GNUC__) && __GNUC__ >= 12 && !defined(__clang__)  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wmaybe-uninitialized"  
#endif  
    // copy/move/assign implementation  
#if defined(__GNUC__) && __GNUC__ >= 12 && !defined(__clang__)  
#pragma GCC diagnostic pop  
#endif  
```  
  
Likely locations in `variant.hpp`:  
- `variant_cc_base_impl` copy constructor (~line 1417)  
- `variant_ma_base_impl` move assignment (~line 1650)  
- `variant_base_impl::emplace` (~line 1122)  
  
### Workaround  
  
Users must add pragmas at every call site that triggers the warning, which is burdensome and error-prone.  
  
### Related  
  
This is a known class of GCC false positives with discriminated union types. Similar issues have been reported for `std::variant`.
