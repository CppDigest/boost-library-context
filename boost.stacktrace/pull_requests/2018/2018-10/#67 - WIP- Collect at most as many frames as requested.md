# #67 WIP: Collect at most as many frames as requested. [Merged]

> Username: JanEisenhauer  
> Created at: 2018-10-29 12:30:29 UTC  
> Updated at: 2018-10-30 07:50:11 UTC  
> Merged at: 2018-10-30 07:49:47 UTC  
> Closed at: 2018-10-30 07:49:47 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67  

Regardless of any user provided maximum stack depth `boost::stacktrace::basic_stacktrace<>::init` will try to obtain 128 stack frames.  
  
For older Microsoft platforms like Windows Server 2003 and Windows XP this is problematic as `RtlCaptureStackBacktrace` has a stricter [limit](https://msdn.microsoft.com/de-de/library/windows/desktop/bb204633(v=vs.85).aspx) on the stack depth.

---

## Review 1 [Commented]

> Username: JanEisenhauer  
> Created_at: 2018-10-29 12:50:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/67#pullrequestreview-169282097  

📁 include/boost/stacktrace/stacktrace.hpp

```diff
  75 |                 native_frame_ptr_t buffer[buffer_size];
  75 |-                 const std::size_t frames_count = boost::stacktrace::detail::this_thread_frames::collect(buffer, buffer_size, frames_to_skip + 1);
  76 |+                 const std::size_t frames_count = boost::stacktrace::detail::this_thread_frames::collect(buffer, std::min(buffer_size, max_depth), frames_to_skip + 1);
```

> Username: JanEisenhauer  
> Created_at: 2018-10-29 12:50:08 UTC  
> Updated_at: 2018-10-29 14:12:39 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67#discussion_r228910556  

Enables users on older Windows platforms to use small enough maximum stack depth.


---

## Comment 2

> Username: coveralls  
> Created_at: 2018-10-29 12:51:08 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67#issuecomment-433898076  

[![Coverage Status](https://coveralls.io/builds/19778745/badge)](https://coveralls.io/builds/19778745)  
  
Coverage remained the same at 90.044% when pulling **5e85e7414ab6006c398385593338dba5e0b9e4c6 on JanEisenhauer:develop** into **4123beb4af6ff4e36769905b87c206da39190847 on boostorg:develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2018-10-29 12:51:08 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67#issuecomment-433898077  

[![Coverage Status](https://coveralls.io/builds/19778745/badge)](https://coveralls.io/builds/19778745)  
  
Coverage remained the same at 90.044% when pulling **5e85e7414ab6006c398385593338dba5e0b9e4c6 on JanEisenhauer:develop** into **4123beb4af6ff4e36769905b87c206da39190847 on boostorg:develop**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2018-10-29 12:51:08 UTC  
> Updated_at: 2018-10-29 15:03:37 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67#issuecomment-433898078  

[![Coverage Status](https://coveralls.io/builds/19781354/badge)](https://coveralls.io/builds/19781354)  
  
Coverage decreased (-0.04%) to 90.0% when pulling **cbd625bd913e2a075a2896db2b6cf8d2fe39f9f8 on JanEisenhauer:develop** into **4123beb4af6ff4e36769905b87c206da39190847 on boostorg:develop**.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2018-10-30 07:50:11 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/67#issuecomment-434203147  

Awesome! Many thanks for the patch!

---
