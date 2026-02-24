# #673 - Hard-to-find warning implicit-fallthrough with NO_EXCEPTIONS [Closed]

> Username: ckormanyos  
> Created at: 2025-04-08 13:21:16 UTC  
> Updated at: 2025-04-11 06:04:18 UTC  
> Closed at: 2025-04-11 06:04:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/673  

Found an interesting, yet hard-to-find, warning today in modular boost multiprecision.  
  
When I compile code with `BOOST_NO_EXCEPTIONS`, there is a line in `cpp_bin_float` that expands differently in this compiler-configuration. And the warning `-Wimplicit-fallthrough` is issued [here](https://github.com/boostorg/multiprecision/blob/02d8862203d0cd95b81c51d0dd30cdffad1965c9/include/boost/multiprecision/cpp_bin_float.hpp#L1617).  
  
The implicit fallthrough is at line 1616. When `BOOST_MP_THROW_EXCEPTION` does not actually throw, the compiler correctly detects the implicit fallthrough and warns on `-Wextra`.  
  
So we can just add the same two lines that the case below has, since this is the behavior that was being fallen through to.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-04-08 13:22:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/673#issuecomment-2786427419  

I can handle this one, but I'll ask for review when it cycles.  
  
Cc: @jzmaddock
