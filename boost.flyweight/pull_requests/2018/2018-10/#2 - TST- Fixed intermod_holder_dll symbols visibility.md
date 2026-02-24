# #2 TST: Fixed intermod_holder_dll symbols visibility [Closed]

> Username: Kojoley  
> Created at: 2018-10-25 13:06:40 UTC  
> Updated at: 2018-10-26 08:14:02 UTC  
> Closed at: 2018-10-26 08:13:40 UTC  
> Url: https://github.com/boostorg/flyweight/pull/2  

Currently test_intermod_holder fails to link across nix systems (look at [boost regression matrix](https://www.boost.org/development/tests/develop/developer/flyweight.html) for flyweight) because of recent switch to `-fvisibility=hidden`.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2018-10-26 08:13:40 UTC  
> Updated_at: 2018-10-26 08:14:02 UTC  
> Url: https://github.com/boostorg/flyweight/pull/2#issuecomment-433326040  

Hi Nikita, thanks for the heads-up. I've used 9dd0c57f46571f545c460f0bb197179bff8ea1b2 (with my preferred formatting) rather than your PR.

---
