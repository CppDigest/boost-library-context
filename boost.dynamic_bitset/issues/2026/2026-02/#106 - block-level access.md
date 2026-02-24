# #106 - block-level access [Open]

> Username: K-ballo  
> Created at: 2026-02-03 09:15:48 UTC  
> Updated at: 2026-02-18 15:52:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/106  

It's currently possible to operate at block-level via `to_block_range()` / `from_block_range()`. It would be useful to have direct access to a single specific block.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2026-02-17 17:11:25 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/106#issuecomment-3915953998  

Sorry for the delayed response. I only noticed the issue just now. In my view, this would break encapsulation. In particular, a user could violate the invariant that all unused bits must be zero. `from_block_range()` already has this problem, and I wouldn't want to make it worse.

---

## Comment 2

> Username: gennaroprota  
> Created at: 2026-02-18 15:52:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/106#issuecomment-3921615033  

I just committed a fix for that `from_block_range()` behavior. See 6cb5ddedbb93f3989c9db326e19bd76bf6bb58ea.
