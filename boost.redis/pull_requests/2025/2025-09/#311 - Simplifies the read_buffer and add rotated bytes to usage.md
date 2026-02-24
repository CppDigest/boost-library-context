# #311 Simplifies the read_buffer and add rotated bytes to usage. [Merged]

> Username: mzimbres  
> Created at: 2025-09-21 20:30:33 UTC  
> Updated at: 2025-09-29 19:50:19 UTC  
> Merged at: 2025-09-29 19:50:19 UTC  
> Closed at: 2025-09-29 19:50:19 UTC  
> Url: https://github.com/boostorg/redis/pull/311  

Data rotation in the read buffer creates latency, we know it is preset but so far its magnitude was unknown. This PR adds it as a new field to `usage`. For example, the `test_conn_echo_stress` outputs now  
  
```  
   Commands sent: 780,002  
   Bytes sent: 32,670,085  
   Responses received: 780,001  
   Pushes received: 750,001  
   Bytes received (response): 3,210,147  
   Bytes received (push): 32,250,036  
   Bytes rotated: 3,109,190,184  
```  
  
In total approximately 34Mb are received but 3Gb are rotated.

---

## Comment 1

> Username: mzimbres  
> Created_at: 2025-09-21 20:35:00 UTC  
> Updated_at: 2025-09-21 20:38:07 UTC  
> Url: https://github.com/boostorg/redis/pull/311#issuecomment-3316247348  

@anarthal, the amount of data rotated looks completely unacceptable (~88 times larger) to me and is probably a big source of latency. Once we merge this I will open a ticket to address that problem in the `read_buffer` by introducing a buffer offset that trades data rotation with memory consumption i.e. `read_buffer_.consume()` will update an offset until a threshold is reached and only then rotate.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-09-21 20:41:38 UTC  
> Url: https://github.com/boostorg/redis/pull/311#issuecomment-3316250495  

3GB vs 34MB? wtf? I'll have a look tomorrow.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-09-21 20:47:47 UTC  
> Url: https://github.com/boostorg/redis/pull/311#issuecomment-3316253304  

Hm, we're rotating unconditionally aren't we? Even if there are N messages cached. For a large N, that's almost an O(N2) rotation in terms of data size.  
  
In MySQL I only rotate when the buffer needs extra space, and not after every message is consumed.

---

## Review 4 [Commented]

> Username: anarthal  
> Created_at: 2025-09-22 11:03:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/311#pullrequestreview-3251866676  

Most of the comments are small observations. My only real question is whether we want to support `usage::rotated_bytes` long-term, or if this is something used internally for benchmarks, only. I'd lean towards the second option. If that's the case, we'd need to clearly state that the member is not for public use, not include it in the reference, or otherwise place it somewhere else, rather than in usage.

📁 include/boost/redis/impl/multiplexer.ipp

```diff
 157 | }
 158 | 
 159 |+ auto multiplexer::prepare_read() noexcept -> system::error_code
```

> Username: anarthal  
> Created_at: 2025-09-22 10:47:25 UTC  
> Updated_at: 2025-09-22 11:03:17 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2367613539  

If these are already defined in an .ipp in read_buffer, it might be more lightweight to define them inline here.


📁 include/boost/redis/usage.hpp

```diff
  39 |+ 
  40 |+    /// Number of bytes rotated in the read buffer.
  41 |+    std::size_t bytes_rotated = 0;
```

> Username: anarthal  
> Created_at: 2025-09-22 10:53:09 UTC  
> Updated_at: 2025-09-22 11:03:17 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2367644532  

Is this something interesting for the user, or just for us?

> Username: mzimbres  
> Created_at: 2025-09-22 19:50:27 UTC  
> Updated_at: 2025-09-22 19:50:28 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2370118138  

The `usage` struct is not useful for the average user, only for those users profiling their app. I don't think they will ever come asking _hey, how much data do you rotate internally_ :) but having the field there make them at least aware there can be some latency hidden there.  At the moment it is entirely meant for us, we will want to compare numbers when we optimize (could actually sax fix) the read buffer.

> Username: anarthal  
> Created_at: 2025-09-23 17:47:43 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2373050938  

Well, if for whatever reason we want to remove it later, we can leave it as zero and go. So I see no real problem.


📁 test/common.hpp

```diff
  47 |+ //
  48 |+ // This is used in the multiplexer tests.
  49 |+ void read(multiplexer& mpx, std::string_view data);
```

> Username: anarthal  
> Created_at: 2025-09-22 10:54:12 UTC  
> Updated_at: 2025-09-22 11:03:17 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2367650435  

Could we split these functions into a separate header? This is including asio/awaitable, which is super heavy.


📁 test/common.cpp

```diff
  82 |+ }
  83 |+ 
  84 |+ void read2(multiplexer& mpx, std::string_view data)
```

> Username: anarthal  
> Created_at: 2025-09-22 11:00:00 UTC  
> Updated_at: 2025-09-22 11:03:17 UTC  
> Url: https://github.com/boostorg/redis/pull/311#discussion_r2367682738  

Is this only used in the reader tests? If so, I'd move it there. Also, read2 is not very semantic - how about making it `copy_to(span<char> to, string_view from)`?


---

## Comment 5

> Username: anarthal  
> Created_at: 2025-09-23 17:48:35 UTC  
> Url: https://github.com/boostorg/redis/pull/311#issuecomment-3324988703  

With that solved, once CI passes and you apply whatever comments you find useful, I think this is good to go.

---
