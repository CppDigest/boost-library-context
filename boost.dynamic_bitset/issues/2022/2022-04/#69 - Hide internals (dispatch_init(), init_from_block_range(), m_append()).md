# #69 - Hide internals (dispatch_init(), init_from_block_range(), m_append()) [Closed]

> Username: jeking3  
> Created at: 2022-04-27 18:13:17 UTC  
> Updated at: 2025-08-18 15:41:11 UTC  
> Closed at: 2025-08-18 15:41:11 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/69  

Details: https://github.com/boostorg/dynamic_bitset/pull/67  
  
There are a few methods that do not need to be public, perhaps, so according to @glenfe :  
  
- In Boost 1.80.0, announce in documentation and in release notes that these functions will only be provisionally public in Boost 1.81.0 if macros are defined.  
- In Boost 1.81.0 these functions will only be public if some macro like BOOST_DYNAMIC_BITSET_DEPRECATED_PUBLIC is defined. The release notes and documentation will state that in Boost 1.83.0 these functions will be unconditionally private.  
- In Boost 1.83.0 the functions will be unconditionally private.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-08-05 10:19:44 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/69#issuecomment-3154591013  

Do we really want to go through all this? It's highly unlikely that those members have been used by anyone.
