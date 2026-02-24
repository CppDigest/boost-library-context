# #313 Silence maybe-uninitialized warning in adj_list_edge_iterator::operator++() [Merged]

> Username: mattpulver  
> Created at: 2022-11-14 16:54:15 UTC  
> Updated at: 2023-07-07 01:49:56 UTC  
> Merged at: 2023-07-07 01:49:56 UTC  
> Closed at: 2023-07-07 01:49:56 UTC  
> Url: https://github.com/boostorg/graph/pull/313  

It's inconclusive whether or not this is a legitimate warning or a GCC 12 false positive. The warning given in #312 appears to only show when built in cmake Release mode.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-11-14 22:28:52 UTC  
> Url: https://github.com/boostorg/graph/pull/313#issuecomment-1314494195  

Now that I see exactly what you mean it does sound suspiciously like a false positive.   
  
I've found one recent discussion about false positives from GCC 12.1 (https://github.com/catchorg/Catch2/issues/2423), so I'm wondering maybe it's better to wait this one out for a while and see if it gets fixed in the 12.x series.

---

## Comment 2

> Username: mattpulver  
> Created_at: 2022-11-14 22:32:36 UTC  
> Url: https://github.com/boostorg/graph/pull/313#issuecomment-1314497148  

Unfortunately I'm seeing this w/ `gcc 12.2.0`. But I'm fine to wait it out. I agree this is not the most desirable hack.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-11-14 22:42:46 UTC  
> Url: https://github.com/boostorg/graph/pull/313#issuecomment-1314505052  

Let's draw a line and say that if it's not fixed by the time 13.1 comes out, then we implement our fix.

---

## Comment 4

> Username: jcelerier  
> Created_at: 2023-06-28 15:00:54 UTC  
> Url: https://github.com/boostorg/graph/pull/313#issuecomment-1611607822  

The warning is still happening with GCC 13.1

---
