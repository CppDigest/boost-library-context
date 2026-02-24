# #666 Fix buffer_iterator_index_expr [Merged]

> Username: jszuppe  
> Created at: 2016-10-10 18:03:04 UTC  
> Updated at: 2016-11-09 07:16:17 UTC  
> Merged at: 2016-11-09 06:00:00 UTC  
> Closed at: 2016-11-09 06:00:00 UTC  
> Url: https://github.com/boostorg/compute/pull/666  

This addresses https://github.com/boostorg/compute/issues/665. I also made all index expression immutable.

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-10-10 22:31:28 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-252763666  

[![Coverage Status](https://coveralls.io/builds/8271617/badge)](https://coveralls.io/builds/8271617)  
  
Coverage increased (+0.002%) to 83.34% when pulling **b9efc690c82ab29bdde152f7a9765c4d3c18743a on haahh:pr_fix_iterators_index_expr** into **10b216f9cd3d46f5d4c9ef1a6e328cf1e728bbc2 on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-10-11 01:21:44 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-252787144  

[![Coverage Status](https://coveralls.io/builds/8273533/badge)](https://coveralls.io/builds/8273533)  
  
Coverage increased (+0.002%) to 83.34% when pulling **b9efc690c82ab29bdde152f7a9765c4d3c18743a on haahh:pr_fix_iterators_index_expr** into **10b216f9cd3d46f5d4c9ef1a6e328cf1e728bbc2 on boostorg:develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-10-11 08:45:57 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-252849753  

[![Coverage Status](https://coveralls.io/builds/8277591/badge)](https://coveralls.io/builds/8277591)  
  
Coverage increased (+0.002%) to 83.34% when pulling **7f3e66c1ddf099286a895b0f7590f892bdf3c6e5 on haahh:pr_fix_iterators_index_expr** into **10b216f9cd3d46f5d4c9ef1a6e328cf1e728bbc2 on boostorg:develop**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-10-11 20:23:17 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-253034377  

[![Coverage Status](https://coveralls.io/builds/8287567/badge)](https://coveralls.io/builds/8287567)  
  
Coverage increased (+0.002%) to 83.34% when pulling **79d90bcd3b72b664fdd0ff899963cc1bbd95e2cb on haahh:pr_fix_iterators_index_expr** into **10b216f9cd3d46f5d4c9ef1a6e328cf1e728bbc2 on boostorg:develop**.

---

## Comment 5

> Username: Ulfgard  
> Created_at: 2016-10-16 09:38:38 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-254037010  

looks good from my side!

---

## Comment 6

> Username: jszuppe  
> Created_at: 2016-11-08 11:06:35 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-259109044  

@kylelutz Can you look at this? This pull request contains fix for OSX build on Travis-CI, and it would be great to have it, so contributors don't see their pull requests fail when they're perfectly fine. If you are not sure about introduced change to `buffer_iterator_index_expr` just tell me, so I'll isolate Travi-CI fix from this pull request.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2016-11-09 06:00:07 UTC  
> Url: https://github.com/boostorg/compute/pull/666#issuecomment-259339752  

Looks good! Thanks!

---
