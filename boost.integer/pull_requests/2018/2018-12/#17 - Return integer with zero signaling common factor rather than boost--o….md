# #17 Return integer with zero signaling common factor rather than boost::o… [Merged]

> Username: NAThompson  
> Created at: 2018-12-04 17:56:05 UTC  
> Updated at: 2018-12-05 10:07:23 UTC  
> Merged at: 2018-12-05 10:06:56 UTC  
> Closed at: 2018-12-05 10:06:56 UTC  
> Url: https://github.com/boostorg/integer/pull/17  

…ptional<Z>.

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2018-12-04 18:19:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/integer/pull/17#pullrequestreview-181406678  

📁 include/boost/integer/mod_inverse.hpp

```diff
  33 |         // a doesn't have a modular multiplicative inverse:
  36 |-         return boost::none;
  34 |+         return 0;
```

> Username: glenfe  
> Created_at: 2018-12-04 18:18:48 UTC  
> Updated_at: 2018-12-04 19:09:43 UTC  
> Url: https://github.com/boostorg/integer/pull/17#discussion_r238779982  

Does this make assumptions about a constructor for `Z` not being explicit? Or should this be `return Z();` or `return Z(0);` instead?

> Username: NAThompson  
> Created_at: 2018-12-04 18:32:56 UTC  
> Updated_at: 2018-12-04 19:09:43 UTC  
> Url: https://github.com/boostorg/integer/pull/17#discussion_r238785055  

Yeah good point. I also explicitly cast the comparisons to type `Z` so that we won't get warnings.


---

## Review 2 [Commented]

> Username: glenfe  
> Created_at: 2018-12-04 18:53:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/integer/pull/17#pullrequestreview-181422404  

📁 include/boost/integer/mod_inverse.hpp

```diff
  38 |-     boost::integer::euclidean_result_t<Z> u = boost::integer::extended_euclidean(a, modulus);
  39 |-     if (u.gcd > 1)
  36 |+     boost::integer::euclidean_result_t<Z> u = extended_euclidean(a, modulus);
```

> Username: glenfe  
> Created_at: 2018-12-04 18:53:51 UTC  
> Updated_at: 2018-12-04 19:09:43 UTC  
> Url: https://github.com/boostorg/integer/pull/17#discussion_r238792746  

Fully qualifying is preferable since you don't intend this to be some form of ADL customization point. i.e. I would keep the `boost::integer::extended_euclidean(a, modulus)` unless you have reasons to prefer otherwise. Rest looks fine to me.

> Username: NAThompson  
> Created_at: 2018-12-04 19:10:08 UTC  
> Url: https://github.com/boostorg/integer/pull/17#discussion_r238798618  

Yup, this somehow got messed up in the merge. Thanks!


---

## Comment 3

> Username: Lastique  
> Created_at: 2018-12-05 10:07:23 UTC  
> Url: https://github.com/boostorg/integer/pull/17#issuecomment-444430281  

Thanks for the PR and the comments.

---
