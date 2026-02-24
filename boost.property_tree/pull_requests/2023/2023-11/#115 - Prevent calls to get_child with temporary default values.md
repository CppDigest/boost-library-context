# #115 Prevent calls to get_child with temporary default values [Merged]

> Username: ashtum  
> Created at: 2023-11-19 08:02:46 UTC  
> Updated at: 2023-11-20 16:32:33 UTC  
> Merged at: 2023-11-20 16:32:33 UTC  
> Closed at: 2023-11-20 16:32:33 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115  

Resolves #42

---

## Comment 1

> Username: ashtum  
> Created_at: 2023-11-19 09:24:50 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817798502  

@pdimov, do you have any suggestions on how to add a test to verify that get_child doesn't compile with temporary default values? Can I use a concept and include it as a test for C++20 and higher? (although it doesn't look elegant)

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-11-19 12:39:51 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817843161  

Use a compile-fail test.

---

## Comment 3

> Username: ashtum  
> Created_at: 2023-11-19 16:06:25 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817898796  

> Use a compile-fail test.  
  
Just out of curiosity, is there any alternative way to test it without using a separate compilation unit? I mean, in complex scenarios, the compilation failure could be due to other reasons as well.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-11-19 17:00:08 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817916115  

There probably is but I wouldn't recommend it.

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-11-19 17:05:57 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817918029  

Something like https://godbolt.org/z/j7MaTGTcd.

---

## Comment 6

> Username: ashtum  
> Created_at: 2023-11-19 17:34:12 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817925772  

> Something like https://godbolt.org/z/j7MaTGTcd.  
  
Do you discourage it because of the possibility of the expression becoming invalid for other reasons and not manifesting itself in the test? If that's the case, can't we mitigate this by also testing valid expressions, like so:  
  
```C++  
static_assert(mp_valid<accepts_default_of_type, ptree&>::value, "");  
static_assert(mp_valid<accepts_default_of_type, const ptree&>::value, "");  
```

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-11-19 17:37:11 UTC  
> Url: https://github.com/boostorg/property_tree/pull/115#issuecomment-1817926470  

It's too complicated, requires specialized knowledge to understand, and is therefore hard to maintain.

---
