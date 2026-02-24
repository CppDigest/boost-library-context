# #185 Add `from_chars_result::operator bool()` [Merged]

> Username: Lastique  
> Created at: 2025-12-26 17:20:22 UTC  
> Updated at: 2025-12-29 14:09:03 UTC  
> Merged at: 2025-12-29 11:08:27 UTC  
> Closed at: 2025-12-29 11:08:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/185  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-12-26 18:37:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/185#pullrequestreview-3613772429  

📁 test/test_from_chars.cpp

```diff
  23 |     BOOST_TEST( r.ec == from_chars_error::none );
  24 |     BOOST_TEST_EQ( static_cast<int>( r.ec ), 0 );
  25 |+     BOOST_TEST( !!r );
```

> Username: pdimov  
> Created_at: 2025-12-26 18:37:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/185#discussion_r2648544138  

This should probably be just `r`, because as-is, it tests `operator!`.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-12-26 18:38:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/185#pullrequestreview-3613773326  

📁 test/test_from_chars_2.cpp

```diff
  23 |     BOOST_TEST( r.ec == err );
  24 |     BOOST_TEST_EQ( static_cast<int>( r.ec ), static_cast<int>( err ) );
  25 |+     if (err == from_chars_error::none)
```

> Username: pdimov  
> Created_at: 2025-12-26 18:38:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/185#discussion_r2648545052  

This could be `BOOST_TEST_EQ( static_cast<bool>( r ), err == from_chars_error::none )`.


---

## Comment 3

> Username: Lastique  
> Created_at: 2025-12-26 18:59:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/185#issuecomment-3693243292  

All done.

---
