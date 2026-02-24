# #1 Silence GCC -Wcast-qual warning [Closed]

> Username: K-ballo  
> Created at: 2014-01-13 17:25:56 UTC  
> Updated at: 2014-07-02 09:00:57 UTC  
> Closed at: 2014-01-13 18:32:34 UTC  
> Url: https://github.com/boostorg/atomic/pull/1  

Use C++ casts instead of C-style casts in order to silence warning about casting away qualifiers.  
  
warning: cast from type 'volatile bool_' to type 'bool_' casts away qualifiers [-Wcast-qual]

---

## Comment 1

> Username: timblechmann  
> Created_at: 2014-01-13 17:28:57 UTC  
> Url: https://github.com/boostorg/atomic/pull/1#issuecomment-32191034  

looks good to me, thanks!  
  
just one thing: can you update the PR to target the "develop" branch instead of "master"?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-01-13 17:31:53 UTC  
> Url: https://github.com/boostorg/atomic/pull/1#issuecomment-32191294  

I don't think I can update it, I could close this pull request and issue a new one. Otherwise, feel free to close it and apply the change yourself.

---
