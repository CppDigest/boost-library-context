# #827 Modernize `x3::skip` [Merged]

> Username: saki7  
> Created at: 2025-09-10 10:10:04 UTC  
> Updated at: 2025-09-11 06:51:32 UTC  
> Merged at: 2025-09-11 06:50:52 UTC  
> Closed at: 2025-09-11 06:50:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/827  

Part of boostorg/spirit_x4#4   
  
## Summary  
- Use concepts in `x3::skip`.  
- Make `x3::skip` a CPO that inhibits ADL.  
- `x3::reskip[...]` new API that has the same effect as `x3::skip[...]`.  
- `x3::skip[...]`: deprecated in favor of `x3::reskip[...]`.  
  
## Renaming `skip[p]` to `reskip[p]`  
 I suspect that the issue like boostorg/spirit#797 is happening because people think `x3::skip` does "skip" something, but actually it does **"reskip"** in certain cases.   
  
- `skip(skipper)[p]`  => Use `skipper` for `p`.  
- `no_skip[skip[p]]` => Re-establish the skipper inhibited by `no_skip`.  
  
IMHO, the second API should definitely be named "reskip" instead of just "skip". This PR deprecates the `operator[]` for this case, and adds the brand new `reskip[...]` for this purpose:  
  
- `no_skip[skip[p]]` => `[[deprecated]]`, but retains the same functionality for now.  
- `no_skip[reskip[p]]` => Re-establish the skipper inhibited by `no_skip`.  
  
We should accept the fact that we named it poorly. I guess we should've done this fix many years ago. To be honest, I remember that I got confused with this naming when I started using Qi. I believe now is the time to fix.  
  
### Success chance of renaming  
I'm not entirely sure how people feel about this change. It's possible that some people rather think "skip" is more intuitive than "reskip". If such complaints emerge, we can undeprecate the rename. (Note: even the C++ standard sometimes do undeprecate features.)  
  
## Documentation  
I understand this change requires documentation. But I have tons of work for boostorg/spirit_x4#4, and I plan to documentation work at the very last stage of modernization. Meanwhile, compiler will emit the helpful warning that says users should migrate to `x3::reskip`, thanks to the `[[deprecated]]` attribute.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-11 06:49:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/827#issuecomment-3278554857  

I had a good sleep last night and thought the rename is looking good. A better API reduces chance to write error-prone or ill-formed application. That lowers the barrier for both beginners and contributors.  
  
We can continue to add these `[[deprecated]]` to alert the end users. If they feel something is wrong, I believe they can submit an issue regarding the naming. I would like to hear from the community, and not planning to _remove_ the old API anytime soon.

---
