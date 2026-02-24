# #284 Range update 2 [Merged]

> Username: vinniefalco  
> Created at: 2022-08-01 01:45:32 UTC  
> Updated at: 2022-08-01 21:47:35 UTC  
> Merged at: 2022-08-01 21:47:20 UTC  
> Closed at: 2022-08-01 21:47:20 UTC  
> Url: https://github.com/boostorg/url/pull/284  

Refine the nuances with `error::end`

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-01 01:54:39 UTC  
> Url: https://github.com/boostorg/url/pull/284#issuecomment-1200593026  

An automated preview of the documentation is available at [https://284.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://284.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-01 20:49:47 UTC  
> Url: https://github.com/boostorg/url/pull/284#issuecomment-1201706078  

An automated preview of the documentation is available at [https://284.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://284.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: alandefreitas  
> Created_at: 2022-08-01 21:19:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/284#pullrequestreview-1057847218  

Another big improvement :)

📁 include/boost/url/rfc/detail/path_rules.hpp

```diff
  10 |- #ifndef BOOST_URL_RFC_DETAIL_SEGMENT_RULE_HPP
  11 |- #define BOOST_URL_RFC_DETAIL_SEGMENT_RULE_HPP
  10 |+ #ifndef BOOST_URL_RFC_DETAIL_PATH_RULES_HPP
```

> Username: alandefreitas  
> Created_at: 2022-08-01 20:49:52 UTC  
> Updated_at: 2022-08-01 21:19:50 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934909154  

Why become path_rules.hpp if all rules here are "segment"?

> Username: vinniefalco  
> Created_at: 2022-08-01 21:20:41 UTC  
> Updated_at: 2022-08-01 21:20:42 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934929021  

because I keep moving stuff around and I will probably move stuff around more, and I got tired of renaming it.


📁 include/boost/url/detail/empty_value.hpp

```diff
   1 |+ /*
```

> Username: alandefreitas  
> Created_at: 2022-08-01 20:50:46 UTC  
> Updated_at: 2022-08-01 21:19:50 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934909760  

Sorry. Why not `boost/core/empty_value.hpp`?

> Username: vinniefalco  
> Created_at: 2022-08-01 21:20:49 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934929084  

not `constexpr`


📁 include/boost/url/grammar/range_rule.hpp

```diff
 126 |+         zero elements.
 127 |+     */
 128 |     range() noexcept = default;
```

> Username: alandefreitas  
> Created_at: 2022-08-01 21:17:21 UTC  
> Updated_at: 2022-08-01 21:19:50 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934926979  

So we explicitly want this now?

> Username: vinniefalco  
> Created_at: 2022-08-01 21:21:33 UTC  
> Updated_at: 2022-08-01 21:21:34 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934929534  

we don't have a choice, because `hier_part_rule::value_type` is default constructible. If we don't allow empty ranges then we have to write a lot more pointless constructors for value types. Anyway it works, so no big deal.


---

## Review 4 [Commented]

> Username: cmazakas  
> Created_at: 2022-08-01 21:23:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/284#pullrequestreview-1057871140  

📁 test/unit/grammar/range_rule.cpp

```diff
  49 |+                 init.begin()));
  50 |+     }
  51 |+ 
```

> Username: cmazakas  
> Created_at: 2022-08-01 21:16:02 UTC  
> Updated_at: 2022-08-01 21:23:18 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934926225  

You may want to refactor this to use instead use:  
```cpp  
BOOST_TEST_ALL_EQ(rv->begin(), rv->end(), init.begin(), init.end());  
```  
which gives some slightly better error diagnostics on failure.

> Username: vinniefalco  
> Created_at: 2022-08-01 21:25:07 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934931727  

I don't think I have that, this isn't Lightweight Test

---

📁 test/unit/grammar/range_rule.cpp

```diff
  61 |+ 
  62 |+         check("", {}, r);
  63 |+         check("x", {"x"}, r);
```

> Username: cmazakas  
> Created_at: 2022-08-01 21:21:31 UTC  
> Updated_at: 2022-08-01 21:23:18 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934929507  

This test doesn't seem exhaustive enough to me. Unless I'm misreading, I'd consider adding further cases:  
```cpp  
check("a+b+c", {"a", "b", "c"}, r);  
check("a+b+", {"a", "b", "c"}, r);  
check("+b+c", {"a", "b", "c"}, r);  
check("a++c", {"a", "b", "c"}, r);  
```  
or something to this effect.

> Username: vinniefalco  
> Created_at: 2022-08-01 21:26:01 UTC  
> Updated_at: 2022-08-01 21:26:02 UTC  
> Url: https://github.com/boostorg/url/pull/284#discussion_r934932363  

Yeah, I am furiously trying to finish implementation, design, and docs - tests are kind of an afterthought since they can wait until after the review. Truth be told, this range gets so much testing from being used for query params and path segments that I am not too worried about it. But yes we will circle back and add more.


---
