# #109 Fix endian issues in md5 name/hash implementation [Merged]

> Username: jeking3  
> Created at: 2019-06-24 13:18:01 UTC  
> Updated at: 2019-06-25 17:21:20 UTC  
> Merged at: 2019-06-25 17:21:15 UTC  
> Closed at: 2019-06-25 17:21:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/109  

This fix contains a breaking change.  
  
- The default hash algorithm if not specified is SHA1, so unless you are specifically choosing MD5 then you have nothing to worry about as none of these changes affect SHA1 name generation.  
  
- The code was not properly handling MD5_Final copy-to-buffer with respect to the overall result of the name generator on little-endian systems.  While the SHA1 result is copied as integers, the MD5 result is copied into a byte buffer backed by integers, and not handled properly w.r.t. endianness.  
  
- The unit test for MD5 was using memcmp instead of accounting for the endianness, thus hiding the original issue.  
  
- Maintaining consistency in a hash-based implementation over time is critical.  
  
- The md5 based name generation has changed to ensure the generation of md5 digests is correct (and the same) on both little and big endian systems.  Previously it was producing different results on little and big endian systems (and unfortunately, if was the little-endian systems that were incorrect).  
  
- Define `BOOST_UUID_COMPAT_PRE_1_71_MD5` to ensure that any MD5 name generated UUIDs come out the same as they did in 1.66 through 1.70.  Note that in this mode little and big endian systems produce a different uuid for md5 name based uuids with the same inputs (which is the defect that was fixed here).

---
