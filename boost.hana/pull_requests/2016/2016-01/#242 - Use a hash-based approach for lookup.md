# #242 [map] Use a hash-based approach for lookup [Closed]

> Username: ricejasonf  
> Created at: 2016-01-21 00:13:21 UTC  
> Updated at: 2016-02-16 20:28:56 UTC  
> Closed at: 2016-02-16 20:28:56 UTC  
> Url: https://github.com/boostorg/hana/pull/242  

Note that this is branched off of my original PR #226.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-01-21 01:41:09 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-173424289  

Sorry I'm going to close this. It was a lot simpler to make new, appropriately names branch off of `benchmarks/map` and cherry-pick the commit from this. I can do a new PR when `make check` is complete.  
  
I would like to know if I even came close to what you guys were talking about regarding the type-level hash, which was the intent of this PR.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-01-21 05:00:10 UTC  
> Updated_at: 2016-01-21 23:53:12 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-173451359  

> Sorry I'm going to close this  
  
Why close this?  
  
> I would like to know if I even came close to what you guys were talking about regarding the type-level hash, which was the intent of this PR.  
  
Yes, this is very close, except that if two hashes collide then we'll get the wrong results. Each bucket has to contain a list of elements whose hash is that of the bucket, and then we'll do a linear search inside the bucket. Or you could use basically any hashing scheme (linear probing or something more exotic), if you wish.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2016-01-21 23:58:49 UTC  
> Updated_at: 2016-01-21 23:59:28 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-173754198  

I was thinking that the point of normalizing the integral constants was to make  
  
``` cpp  
m[size_c<5>] == m[int_c<5>]  
```  
  
If the buckets contain a list of elements that are linearly searched using `Comparable` then it seems pointless to put `int_c<5>` and `size_c<5>` in the same bucket. Is there ever a case where two `IntegralConstants` will be equal but have different types?  
  
I would like to add some tests for the above when you get back to me on the desired behaviour. I would also add tests to check that map ultimately uses Comparable, but I argue that there is no realistic use that justifies it.

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-01-22 00:08:20 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-173755774  

> I was thinking that the point of normalizing the integral constants was to make  
>   
> ``` c++  
> m[size_c<5>] == m[int_c<5>]  
> ```  
  
Correct. More specifically, the requirement is that if `hana::equal(x, y)`, then `hana::equal(hash(x), hash(y))`. This ensures that we fall into the right bucket all the time, since the hash of a value determines the bucket in which it will end up in the map.  
  
> If the buckets contain a list of elements that are linearly searched using Comparable then it seems pointless to put int_c<5> and size_c<5> in the same bucket.   
  
Looking up in such a map is a two-step process. First, we compute the hash of the key, which gives us the bucket. Now, many different keys can have the same hash, and hence fall into the same bucket, since the hash is not expected to be perfect (collision free). Once we've found the bucket, we then do a linear search **within** this bucket to find the key that we're interested in. If `hash(int_c<5>) != hash(long_c<5>)`, then they will fall into different buckets, and we'll never find `int_c<5>` if we search for `long_c<5>` (and vice versa).  
  
> Is there ever a case where two IntegralConstants will be equal but have different types?  
  
Yes, for example `int_c<5> == long_c<5>`, but `decltype(int_c<5>) != decltype(long_c<5>)`.

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2016-01-24 07:21:43 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-174262474  

`ef2f06f` I made the buckets contain indices of map's storage a tuple.  With regard to `benchmark.at_key.lookup`, there is a greater upfront cost, but lookups appear to scale better (4s vs 21s @n=200). `find` and `erase_key` use the key based lookup so I expect they will perform better as well. The other functions should be unchanged from master as the map's storage is once again a `tuple`.

---

## Comment 6

> Username: ricejasonf  
> Created_at: 2016-01-24 07:27:37 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-174262882  

BTW, `detail::hash_table` could easily be made the back-end for `hana::set` as well.

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2016-01-29 22:12:15 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-176992429  

For my particular use case I have a better way than trying to use sets as keys. Since sets or other containers as keys would probably always result in a linear search, then perhaps it is better to delete the default implementation for `hash` so if people try to use something as a key they know what they are getting into.

---

## Comment 8

> Username: ricejasonf  
> Created_at: 2016-01-29 23:39:44 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-177019420  

I tried using `all_of` to check if all of the keys were "fast", meaning they could never have a hash collision. It shaves about 10% off of the `at_key.index_of_lookup` benchmark, but loses the ability to check for duplicates at creation time. I won't bother pushing, but I thought it was worth mentioning.

---

## Comment 9

> Username: ricejasonf  
> Created_at: 2016-01-29 23:52:00 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-177022784  

I just pushed `295f5fc` to fix `hash` for `type_tag`.

---

## Comment 10

> Username: ldionne  
> Created_at: 2016-02-16 20:28:55 UTC  
> Url: https://github.com/boostorg/hana/pull/242#issuecomment-184863531  

Superseded by #247, where latest changes on the Hashable have been made. #247 also shown a small compile-time speedup over this PR, due to different techniques being used.

---
