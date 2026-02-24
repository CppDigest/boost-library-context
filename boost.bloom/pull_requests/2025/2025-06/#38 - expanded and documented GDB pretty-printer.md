# #38 expanded and documented GDB pretty-printer [Merged]

> Username: joaquintides  
> Created at: 2025-06-27 14:49:09 UTC  
> Updated at: 2025-06-29 15:00:06 UTC  
> Merged at: 2025-06-29 15:00:03 UTC  
> Closed at: 2025-06-29 15:00:03 UTC  
> Url: https://github.com/boostorg/bloom/pull/38  

@k3DW playing locally with the pretty-printer, I've realized that the array display is of little use because it's limited by GDB to a few elements and these filters are generally very large (hundreds of thousands or millions of bytes). To remedy this, I've implemented a custom `operator[]` Xmethod used like this:  
  
https://38.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#tutorial_gdb_pretty_printer  
  
Please review thoroughly as I'm not an expert in this area. Also, you may have ideas about better interfaces than this one. If/when eveything's correct could you please update `bloom_printers.hpp` accordingly in this same branch? Thank you!

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-27 14:55:39 UTC  
> Url: https://github.com/boostorg/bloom/pull/38#issuecomment-3013358205  

An automated preview of the documentation is available at [https://38.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://38.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 2 [Commented]

> Username: k3DW  
> Created_at: 2025-06-29 10:13:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/38#pullrequestreview-2969191460  

Looks great overall. I'll add the prefix "Error: " to the 2 places, and I'll regenerate the header and push to this commit. I can remove that commit if there are any issues

📁 extra/boost_bloom_printers.py

```diff
  68 |+         fp = BoostBloomFilterPrinter(obj)
  69 |+         if fp.array_size == 0:
  70 |+             print('Filter is null')
```

> Username: k3DW  
> Created_at: 2025-06-29 10:12:08 UTC  
> Updated_at: 2025-06-29 10:13:43 UTC  
> Url: https://github.com/boostorg/bloom/pull/38#discussion_r2173700852  

I might consider something like "Error: Filter is null", just to be clear to the use. But that's a nit


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-06-29 10:25:40 UTC  
> Url: https://github.com/boostorg/bloom/pull/38#issuecomment-3016563651  

An automated preview of the documentation is available at [https://38.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://38.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---
