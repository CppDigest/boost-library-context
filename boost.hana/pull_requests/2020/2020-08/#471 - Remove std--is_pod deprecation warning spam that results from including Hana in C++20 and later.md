# #471 Remove std::is_pod deprecation warning spam that results from including Hana in C++20 and later. [Closed]

> Username: tzlaine  
> Created at: 2020-08-29 21:26:48 UTC  
> Updated at: 2020-09-02 23:14:27 UTC  
> Closed at: 2020-09-02 23:14:27 UTC  
> Url: https://github.com/boostorg/hana/pull/471  



---

## Comment 1

> Username: ldionne  
> Created_at: 2020-09-02 17:55:57 UTC  
> Url: https://github.com/boostorg/hana/pull/471#issuecomment-685900076  

Thanks for the PR. Can you make it against `develop`?

---

## Review 2 [Changes requested]

> Username: ldionne  
> Created_at: 2020-09-02 18:00:51 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/471#pullrequestreview-481109377  

📁 include/boost/hana/config.hpp

```diff
 181 |+     //! Without defining this macro, `hana::traits::is_pod` will not be
 182 |+     //! available in C++20 and later.
 183 |+ #   define BOOST_HANA_CONFIG_ENABLE_DEPRECATED_IS_POD
```

> Username: ldionne  
> Created_at: 2020-09-02 17:59:36 UTC  
> Updated_at: 2020-09-02 18:00:51 UTC  
> Url: https://github.com/boostorg/hana/pull/471#discussion_r482261895  

TBH, I would just drop `hana::is_pod` entirely and add an entry in `RELEASE_NOTES.md`. I don't think it's worth adding a knob to tweak that, as it's really easy to get back a similar tool with `hana::trait<std::is_pod>`.


📁 include/boost/hana/traits.hpp

```diff
  68 |     constexpr auto is_trivially_copyable = detail::hana_trait<std::is_trivially_copyable>{};
  69 |     constexpr auto is_standard_layout = detail::hana_trait<std::is_standard_layout>{};
  70 |+ #if __cplusplus < 202002L || defined(BOOST_HANA_CONFIG_ENABLE_DEPRECATED_IS_POD)
```

> Username: ldionne  
> Created_at: 2020-09-02 18:00:42 UTC  
> Updated_at: 2020-09-02 18:00:51 UTC  
> Url: https://github.com/boostorg/hana/pull/471#discussion_r482262551  

I think I'd just drop it altogether. Being Boost and not the standard, I think it's more reasonable to be slightly more aggressive about these things. WDYT?


---

## Comment 3

> Username: tzlaine  
> Created_at: 2020-09-02 23:14:27 UTC  
> Url: https://github.com/boostorg/hana/pull/471#issuecomment-686086239  

Superceded.

---
