# #4 Drop dependency on MPL (for TypeTraits) and Test (for LightweightTest) [Merged]

> Username: glenfe  
> Created at: 2018-09-15 01:36:09 UTC  
> Updated at: 2018-09-15 02:29:24 UTC  
> Merged at: 2018-09-15 02:29:23 UTC  
> Closed at: 2018-09-15 02:29:23 UTC  
> Url: https://github.com/boostorg/crc/pull/4  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-09-15 01:52:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/crc/pull/4#pullrequestreview-155699741  

📁 test/crc_test.cpp

```diff
 124 |-     BOOST_CHECK( slow_crc.checksum() == expected );
 125 |-     BOOST_CHECK( func_result == expected );
 123 |+     BOOST_TEST( fast_crc.checksum() == expected );
```

> Username: pdimov  
> Created_at: 2018-09-15 01:52:00 UTC  
> Updated_at: 2018-09-15 02:13:12 UTC  
> Url: https://github.com/boostorg/crc/pull/4#discussion_r217872641  

BOOST_TEST_EQ here?

> Username: glenfe  
> Created_at: 2018-09-15 02:13:40 UTC  
> Url: https://github.com/boostorg/crc/pull/4#discussion_r217873154  

Done.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-09-15 01:55:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/crc/pull/4#pullrequestreview-155699837  

📁 test/crc_test2.cpp

```diff
  50 | // Conversion functions between native- and big-endian representations
  57 |- #ifdef BOOST_BIG_ENDIAN
  51 |+ #ifdef BOOST_BIG_ENDIAN_BYTE
```

> Username: pdimov  
> Created_at: 2018-09-15 01:55:18 UTC  
> Updated_at: 2018-09-15 02:13:12 UTC  
> Url: https://github.com/boostorg/crc/pull/4#discussion_r217872710  

#if here. BOOST_*_ENDIAN_BYTE are always defined.

> Username: pdimov  
> Created_at: 2018-09-15 01:56:19 UTC  
> Updated_at: 2018-09-15 02:13:12 UTC  
> Url: https://github.com/boostorg/crc/pull/4#discussion_r217872733  

Also, it's BOOST_ENDIAN_BIG_BYTE. :-)

> Username: glenfe  
> Created_at: 2018-09-15 02:13:28 UTC  
> Url: https://github.com/boostorg/crc/pull/4#discussion_r217873147  

Ah. :)


---

## Comment 3

> Username: pdimov  
> Created_at: 2018-09-15 02:28:48 UTC  
> Url: https://github.com/boostorg/crc/pull/4#issuecomment-421524397  

Looks good.

---
