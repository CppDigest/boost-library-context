# #166 Add a conditional rule for enabling boost_stacktrace_from_exception. … [Merged]

> Username: pdimov  
> Created at: 2024-05-25 14:24:23 UTC  
> Updated at: 2024-05-27 08:39:11 UTC  
> Merged at: 2024-05-27 08:38:17 UTC  
> Closed at: 2024-05-27 08:38:17 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/166  

…Fixes #165.  
  
As explained in #165, boost_stacktrace_from_exception doesn't build under macos-14, which breaks CI. Since https://github.com/boostorg/stacktrace/issues/163 claims that it never builds on non-x86 architectures, this PR changes build/Jamfile to only build boost_stacktrace_from_exception by default when the architecture is x86. The feature `boost.stacktrace.from_exception` can still be used to override the default, in both directions.

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-05-25 14:51:20 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/166#issuecomment-2131300913  

[![Coverage Status](https://coveralls.io/builds/67703433/badge)](https://coveralls.io/builds/67703433)  
  
coverage: 86.308%. remained the same  
when pulling **f474b81a3ca236785e37d57097e4d4b30a2b1a5e on pr/issue-165**  
into **39afcefb6413b773b9f464689e994698a7a2ddc8 on develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-05-27 08:39:10 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/166#issuecomment-2132962961  

Many thanks!

---
