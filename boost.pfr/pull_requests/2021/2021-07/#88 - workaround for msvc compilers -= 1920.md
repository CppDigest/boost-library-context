# #88 workaround for msvc compilers <= 1920 [Merged]

> Username: alexey-romanov  
> Created at: 2021-07-14 08:58:36 UTC  
> Updated at: 2021-07-24 18:35:05 UTC  
> Merged at: 2021-07-24 18:35:05 UTC  
> Closed at: 2021-07-24 18:35:05 UTC  
> Url: https://github.com/boostorg/pfr/pull/88  

msvc compilers <= 1920 (VS2017 family compilers and below) have a limit on max 1024 elements in template parameter pack. Limit `max_fields_count` for this particular case.

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2021-07-15 07:47:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/88#pullrequestreview-707029268  

📁 include/boost/pfr/detail/fields_count.hpp

```diff
  13 | #include <boost/pfr/detail/unsafe_declval.hpp>
  14 | 
  15 |+ #include <boost/integer/static_min_max.hpp>
```

> Username: apolukhin  
> Created_at: 2021-07-15 07:44:39 UTC  
> Updated_at: 2021-07-15 07:47:32 UTC  
> Url: https://github.com/boostorg/pfr/pull/88#discussion_r670216785  

Please, don't include other boost librariy headers.  
  
PFR should not depend on other parts of boost

> Username: alexey-romanov  
> Created_at: 2021-07-15 09:22:55 UTC  
> Url: https://github.com/boostorg/pfr/pull/88#discussion_r670291355  

ACK, done.

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 300 |+ #if defined(_MSC_VER) && (_MSC_VER <= 1920)
 301 |+     // Workaround for msvc compilers. Versions <= 1920 have a limit of max 1024 elements in template parameter pack
 302 |+     constexpr std::size_t max_fields_count = boost::static_unsigned_min<sizeof(type) * CHAR_BIT, 1024>::value;
```

> Username: apolukhin  
> Created_at: 2021-07-15 07:46:50 UTC  
> Updated_at: 2021-07-15 07:47:00 UTC  
> Url: https://github.com/boostorg/pfr/pull/88#discussion_r670218300  

```diff  
-    constexpr std::size_t max_fields_count = boost::static_unsigned_min<sizeof(type) * CHAR_BIT, 1024>::value;  
+    constexpr std::size_t max_fields_count = (sizeof(type) * CHAR_BIT >= 1024 ? 1024 : sizeof(type) * CHAR_BIT);  
```  
No very elegant, but works

> Username: alexey-romanov  
> Created_at: 2021-07-15 09:24:51 UTC  
> Updated_at: 2021-07-15 09:25:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/88#discussion_r670292793  

ok, file updated


---

## Comment 2

> Username: alexey-romanov  
> Created_at: 2021-07-24 13:13:36 UTC  
> Url: https://github.com/boostorg/pfr/pull/88#issuecomment-886052074  

@apolukhin changeset updated, please re-review

---

## Review 3 [Approved]

> Username: apolukhin  
> Created_at: 2021-07-24 18:34:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/pfr/pull/88#pullrequestreview-714252916  

Looks good, many thanks!

---
