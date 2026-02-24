# #102 - Could we have a find_prev() as well? [Open]

> Username: rhalbersma  
> Created at: 2025-12-30 15:53:22 UTC  
> Updated at: 2026-01-16 10:23:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/102  

Hi! Could we have a `find_prev()` to go along the `find_next()`? That would allow efficient reverse iteration over all 1-bits. The code should be pretty much the reverse of `find_next()`, and using `countl_zero` instead of `countr_zero` as the bit intrinsic function.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2026-01-05 16:38:31 UTC  
> Updated at: 2026-01-05 16:43:25 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/102#issuecomment-3711211977  

Hi Rein! Thanks for the suggestion :-).  
  
Apparently, some people are confused by the name of `find_first_off()` (they mix it up with `find_first_of()`, although the latter wouldn't make much sense for a bitset), so I'm thinking of the following renames:  
  
- `find_first()` → `find_first_one()`  
- `find_first_off()` → `find_first_zero()`  
- `find_next()` → `find_next_one()`  
- `find_next_off()` → `find_next_zero()`  
  
Once I have decided on that, I'll implement the member you suggested. It will likely be named `find_previous_one()`.

---

## Comment 2

> Username: gennaroprota  
> Created at: 2026-01-09 15:24:02 UTC  
> Updated at: 2026-01-16 10:23:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/102#issuecomment-3729372391  

@rhalbersma: I sketched an implementation in PR #103. It adds a `find_last_one()`, too, although that's logically unnecessary, because a `b.find_previous_one( i )` with `i >= b.size()` (e.g. `i == npos`) would find the last 1 bit. Please let me know what you think.
