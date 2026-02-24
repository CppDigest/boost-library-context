# #30 - Consider SMHasher3 as a resource for hash algorithms [Open]

> Username: fwojcik  
> Created at: 2024-12-22 01:26:39 UTC  
> Updated at: 2024-12-22 02:19:04 UTC  
> Url: https://github.com/boostorg/hash2/issues/30  

Hi! Congratulations on your acceptance into Boost!  
  
I saw somewhere that you were considering adding additional non-cryptographic hashes to Hash2, such as XXH3. I would like to present my SMHasher3 project (https://gitlab.com/fwojcik/smhasher3) as a source of information about additional hashes to consider. There are some quite fast hashes which pass every test (https://gitlab.com/fwojcik/smhasher3/-/tree/main/results?ref_type=heads). Hashes also have metadata attached to them, so you could include or exclude hashes based on features, such as seeding, math features used, or licensing.  
  
More reporting will be added "soon", such as code size reports, and more inlined hash testing. Also, if you have suggestions for additional hash tests, perhaps based on Hash2 usage patterns, I would absolutely consider adding them.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-12-22 01:50:36 UTC  
> Url: https://github.com/boostorg/hash2/issues/30#issuecomment-2558296046  

Interesting, thanks. I knew about rurban/smhasher (and used it to test https://github.com/pdimov/mulxp_hash), but wasn't aware of your fork.

---

## Comment 2

> Username: fwojcik  
> Created at: 2024-12-22 02:19:03 UTC  
> Url: https://github.com/boostorg/hash2/issues/30#issuecomment-2558303416  

I will be sure to add your mulxp_hash to my next release!
