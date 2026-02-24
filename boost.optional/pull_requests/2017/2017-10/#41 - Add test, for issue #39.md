# #41 Add test, for issue #39 [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 21:42:53 UTC  
> Updated at: 2017-11-01 16:54:08 UTC  
> Closed at: 2017-11-01 16:54:08 UTC  
> Url: https://github.com/boostorg/optional/pull/41  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2017-10-30 01:35:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/41#pullrequestreview-72720836  

📁 test/optional_test_has_trivial_ctor.cpp

```diff
  36 |+ void test_has_trivial_default_constructor()
  37 |+ {
  38 |+     optional<std::pair<U, int> > o;
```

> Username: pdimov  
> Created_at: 2017-10-30 01:35:04 UTC  
> Updated_at: 2017-11-01 16:53:34 UTC  
> Url: https://github.com/boostorg/optional/pull/41#discussion_r147604837  

Needs `#ifndef BOOST_NO_CXX11_VARIADIC_TEMPLATES` here.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2017-10-30 01:35:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/41#pullrequestreview-72720867  

📁 test/optional_test_has_trivial_ctor.cpp

```diff
  38 |+     optional<std::pair<U, int> > o;
  39 |+     o.emplace(0, 1);
  40 |+     BOOST_TEST(o);
```

> Username: pdimov  
> Created_at: 2017-10-30 01:35:35 UTC  
> Updated_at: 2017-11-01 16:53:34 UTC  
> Url: https://github.com/boostorg/optional/pull/41#discussion_r147604863  

`#endif` here.


---
