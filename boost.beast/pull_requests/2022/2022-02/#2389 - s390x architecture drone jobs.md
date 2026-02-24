# #2389 s390x architecture drone jobs [Merged]

> Username: sdarwin  
> Created at: 2022-02-09 22:21:41 UTC  
> Updated at: 2022-10-17 17:45:33 UTC  
> Merged at: 2022-10-17 17:45:32 UTC  
> Closed at: 2022-10-17 17:45:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2389  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-06-03 19:37:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1146299116  

Why is this failing?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2022-06-03 19:59:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1146315571  

There might be issues with the specific commit this branch diverged from.    I will rebase and push to the pull request again.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-03 20:26:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1146334978  

![pullrequest](https://2389.beast.tracing.cppalliance.org/pullrequest-7e20dd78.png)  
Timeline tracing charts: [https://2389.beast.tracing.cppalliance.org/index.html](https://2389.beast.tracing.cppalliance.org/index.html)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-06-21 23:36:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1162463067  

@alandefreitas if this doesn't fail we should merge it

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-06-21 23:40:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1162465075  

It does fail in the new architecture.

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-06-22 18:17:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1163458576  

> the tests try to link to coroutine, which doesn't work  
  
(Peter)

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 12:08:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1264627792  

Boost context supports [s390](https://github.com/boostorg/context/blob/develop/src/asm/make_s390x_sysv_elf_gas.S) - will investigate.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 12:45:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1264635304  

@sdarwin it looks like boost.context needs you to explicitly add `architecture=s390x` for it to pick up the right assembly file. I could possibly hack something together, but I reckon it's better if you add that parameter the proper way.  
  
https://github.com/boostorg/context/blob/5852b493554ba8be5459147abf226f57d6bfa74c/build/Jamfile.v2#L473

---

## Comment 9

> Username: sdarwin  
> Created_at: 2022-10-03 13:42:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1265460845  

> needs you to explicitly add architecture=s390x  
  
@klemens-morgenstern it does sound like a good idea. boostorg/boost-ci is missing an option for 'architecture' so I added that and tested. Here are results. https://drone.cpp.al/samd2/beast/239    
As before, s390x gcc is being detected and completes successfully.  It works even without specifying architecture.    
s390x clang fails to be detected. This happens even when specifying architecture.   In the drone job logs, here is what is being executed:  
  
```  
./b2 libs/beast/test toolset=clang cxxstd=11,14,17,20 architecture=s390x variant=release -j4  
```

---

## Comment 10

> Username: klemens-morgenstern  
> Created_at: 2022-10-04 01:02:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1266271087  

Oh, that's an issue in the build script of `context` - I'll create a PR.

---

## Comment 11

> Username: sdarwin  
> Created_at: 2022-10-11 19:10:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1275153449  

@klemens-morgenstern nice work. Switching from clang-12 to clang-14 solved it. However the update to boostorg/context is still required, and they haven't merged it, so this pull request will still show errors.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-10-11 19:26:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1275171166  

![pullrequest](https://2389.beast.tracing.cppalliance.org/pullrequest-4365feaf.png)  
Timeline tracing charts: [https://2389.beast.tracing.cppalliance.org/index.html](https://2389.beast.tracing.cppalliance.org/index.html)

---

## Comment 13

> Username: codecov[bot]  
> Created_at: 2022-10-17 17:33:57 UTC  
> Updated_at: 2022-10-17 17:43:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2389#issuecomment-1281218844  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2389](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1bfad42) into [develop](https://codecov.io/gh/boostorg/beast/commit/5799f159c588c3a4b886d0f8d4dd3e0c1fd42bed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5799f15) will **increase** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2389/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2389      +/-   ##  
===========================================  
+ Coverage    94.72%   94.74%   +0.01%       
===========================================  
  Files          152      153       +1       
  Lines        11858    12066     +208       
===========================================  
+ Hits         11233    11432     +199       
- Misses         625      634       +9       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19hZGFwdG9yLmhwcA==) | `97.80% <0.00%> (-0.87%)` | :arrow_down: |  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `85.71% <0.00%> (-0.78%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `73.78% <0.00%> (-0.71%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.68%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `98.44% <0.00%> (-0.15%)` | :arrow_down: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/core/rate\_policy.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3JhdGVfcG9saWN5LmhwcA==) | `80.00% <0.00%> (ø)` | |  
| [include/boost/beast/core/string\_type.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZ190eXBlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [15 more](https://codecov.io/gh/boostorg/beast/pull/2389/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5799f15...1bfad42](https://codecov.io/gh/boostorg/beast/pull/2389?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
