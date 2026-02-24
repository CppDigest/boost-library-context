# #4 Replace deprected/removed C++98 function adapters by inline type defi… [Merged]

> Username: DanielaE  
> Created at: 2017-05-16 17:29:55 UTC  
> Updated at: 2017-12-28 09:09:58 UTC  
> Merged at: 2017-07-04 18:27:55 UTC  
> Closed at: 2017-07-04 18:27:55 UTC  
> Url: https://github.com/boostorg/xpressive/pull/4  

…nitions.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Commented]

> Username: ericniebler  
> Created_at: 2017-05-16 19:20:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/xpressive/pull/4#pullrequestreview-38486878  

📁 test/test.hpp

```diff
 130 |-   : std::unary_function<xpr_test_case<BidiIter>, void>
 130 | {
 131 |+     typedef BidiIter argument_type;
```

> Username: ericniebler  
> Created_at: 2017-05-16 19:20:52 UTC  
> Updated_at: 2017-05-17 05:03:28 UTC  
> Url: https://github.com/boostorg/xpressive/pull/4#discussion_r116833818  

This doesn't look right to me.

> Username: DanielaE  
> Created_at: 2017-05-17 05:07:24 UTC  
> Url: https://github.com/boostorg/xpressive/pull/4#discussion_r116911347  

You're so right. I hate this copy-and-paste thing so much: neither proof-reading nor any of my compilers spotted this in any of the testsuite runs.  
  
Fixed and passes testsuite on msvc 9.0, 12.0, and 14.1


---

## Comment 2

> Username: Lastique  
> Created_at: 2017-07-13 18:03:21 UTC  
> Url: https://github.com/boostorg/xpressive/pull/4#issuecomment-315156338  

Thanks Eric for merging this PR. Could you also merge it to master, please?

---

## Comment 3

> Username: ericniebler  
> Created_at: 2017-07-25 18:31:25 UTC  
> Url: https://github.com/boostorg/xpressive/pull/4#issuecomment-317828133  

> Thanks Eric for merging this PR. Could you also merge it to master, please?  
  
Done.

---
