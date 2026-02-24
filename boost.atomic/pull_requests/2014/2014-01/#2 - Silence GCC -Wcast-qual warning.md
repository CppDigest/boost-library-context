# #2 Silence GCC -Wcast-qual warning [Merged]

> Username: K-ballo  
> Created at: 2014-01-13 18:32:27 UTC  
> Updated at: 2014-07-16 01:23:15 UTC  
> Merged at: 2014-01-13 18:45:56 UTC  
> Closed at: 2014-01-13 18:45:56 UTC  
> Url: https://github.com/boostorg/atomic/pull/2  

Use C++ casts instead of C-style casts in order to silence warning about casting away qualifiers.  
  
warning: cast from type 'volatile bool_' to type 'bool_' casts away qualifiers [-Wcast-qual]

---
