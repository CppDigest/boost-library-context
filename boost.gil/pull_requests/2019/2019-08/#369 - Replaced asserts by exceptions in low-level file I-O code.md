# #369 Replaced asserts by exceptions in low-level file I/O code [Merged]

> Username: xjhouska  
> Created at: 2019-08-07 12:10:04 UTC  
> Updated at: 2019-08-09 15:54:44 UTC  
> Merged at: 2019-08-09 08:04:18 UTC  
> Closed at: 2019-08-09 08:04:18 UTC  
> Url: https://github.com/boostorg/gil/pull/369  

### Description  
  
The change removes asserts from low-level file I/O routines and replaces them by exceptions. This allows better handling of runtime conditions such as zero-length file or disk full.  
  
### References  
  
https://github.com/boostorg/gil/issues/360  
  
### Tasklist  
  
- [ ] Add test case(s) - see #371  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-08-07 19:31:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/369#pullrequestreview-272177711  

@xjhouska This looks good. I'll give @stefanseefeld and @chhenning two or three days in case they'd have any comments. Then, I'm happy to merge it.  
  
Thank you very much for your contribution and help in making GIL better.  
  
------  
  
~~One question: if possible, could you share the JPEG file from your tests that you used to reveal the problem? It would be good to include it in GIL tests.~~ Solved by https://github.com/boostorg/gil/pull/369#discussion_r311733511

📁 include/boost/gil/io/device.hpp

```diff
  63 |     file_stream_device( const std::string& file_name
  67 |-                       , read_tag   = read_tag()
  64 |+                       , read_tag tag  = read_tag()
```

> Username: mloskot  
> Created_at: 2019-08-07 19:22:27 UTC  
> Updated_at: 2019-08-07 19:31:11 UTC  
> Url: https://github.com/boostorg/gil/pull/369#discussion_r311722613  

Good catch!

---

📁 include/boost/gil/io/device.hpp

```diff
 421 |     {
 438 |-         io_error( "Bad io error." );
 422 |+         io_error( "istream_device: Bad io error." );
```

> Username: mloskot  
> Created_at: 2019-08-07 19:26:34 UTC  
> Updated_at: 2019-08-07 19:31:11 UTC  
> Url: https://github.com/boostorg/gil/pull/369#discussion_r311724184  

I appreciate you've taken care of such details as clarifying the exception messages here and in other places.

> Username: xjhouska  
> Created_at: 2019-08-07 19:51:33 UTC  
> Url: https://github.com/boostorg/gil/pull/369#discussion_r311733511  

The JPEG file that was causing the assert was a zero-length file with extension .jpg , easy to be created by "touch zerolengthfile.jpg".

> Username: mloskot  
> Created_at: 2019-08-07 19:53:44 UTC  
> Url: https://github.com/boostorg/gil/pull/369#discussion_r311734470  

Yes, easy. I wasn't certain if it is zero-file or perhaps a degenerate file with JPEG signature only or something alike.


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-08-09 15:42:38 UTC  
> Url: https://github.com/boostorg/gil/pull/369#issuecomment-519967234  

@xjhouska I'm afraid, we've missed the closing date for changes to Boost 1.71, as per https://www.boost.org/development/index.html  
  
> Boost 1.71.0 closed  
> When: Wednesday, Aug 7, 2019  
> Description: Release closed for all changes, except by permission of a release manager.  
  
So, your fix will be released with Boost 1.72 in December, 2019.  
Meanwhile, you'll need to use Boost 1.71 (or any you desire) with your patch applied manually.

---

## Comment 3

> Username: xjhouska  
> Created_at: 2019-08-09 15:54:44 UTC  
> Url: https://github.com/boostorg/gil/pull/369#issuecomment-519971327  

Never mind, at the moment I'm bound to 1.70 anyway, due to interoperability with other teams who are using 1.70. But thanks for letting me know.

---
