# #2318 Use zlib::Flush::sync instead of zlib::Flush::full, for stream compre… [Closed]

> Username: chreniuc  
> Created at: 2021-09-23 13:33:54 UTC  
> Updated at: 2021-11-03 21:42:43 UTC  
> Closed at: 2021-11-03 21:42:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2318  

…ssion  
  
https://github.com/boostorg/beast/issues/2313

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-09-23 14:06:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-925849035  

![pullrequest](https://2318.beast.tracing.cppalliance.org/pullrequest-e7852673.png)  
Timeline tracing charts: [https://2318.beast.tracing.cppalliance.org/index.html](https://2318.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-09-23 14:28:07 UTC  
> Updated_at: 2021-09-23 14:28:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-925868342  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2318](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c29ee42) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2318/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2318      +/-   ##  
===========================================  
- Coverage    94.88%   94.85%   -0.04%       
===========================================  
  Files          151      151                
  Lines        12230    12230                
===========================================  
- Hits         11605    11601       -4       
- Misses         625      629       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2318/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `86.48% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2318/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.49% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...c29ee42](https://codecov.io/gh/boostorg/beast/pull/2318?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: chreniuc  
> Created_at: 2021-09-24 12:27:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-926585362  

I've noticed that this failed, due to lower code coverage. But its a little bit confusing, it shows that the code coverage dropped in [this file](https://app.codecov.io/gh/boostorg/beast/compare/2318/changes)(`include/boost/beast/websocket/impl/read.hpp`). Which was not modified by me, and also that file doesn't use the `deflate()` method that was changed.

---

## Comment 4

> Username: madmongo1  
> Created_at: 2021-09-24 13:06:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-926610209  

This has passed all tests. Thank you for your valuable contribution.   
  
I would welcome a suggestion on a test I could implement to prove that this is a fix/improvement.  
  
R

---

## Comment 5

> Username: chreniuc  
> Created_at: 2021-09-24 13:55:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-926645012  

The method that I've changed is [this](https://github.com/boostorg/beast/blob/9f2b0ce1db9868b9ec54ebed18bebced53e76782/include/boost/beast/websocket/detail/impl_base.hpp#L83), I tried to find it's related test file, but I only found [this](https://github.com/boostorg/beast/blob/develop/test/beast/websocket/_detail_impl_base.cpp), which is empty.  
   
 I saw that `deflate()` is used in [this file](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/impl/write.hpp#L462), when `write_some_op::operator()` is called  or when [write_some](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/impl/write.hpp#L608) is called, so I think the test might be done [here](https://github.com/boostorg/beast/blob/develop/test/beast/websocket/write.cpp) somehow.  
   
The test that I was thinking about if we were to only test the `deflate()` method:  
 - start with `Hello` string  
 - call `deflate()`, the resulted string will be 7 bytes or will have this content: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
 - call `deflate()` with the same string, and the result will be 5 bytes or will have this content: `0xf2 0x00 0x11 0x00 0x00`  
   
 Which means that compressing the same string, results has two different outputs, the second time the size is smaller.  
   
A better test would be to test this in the [write_some](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/impl/write.hpp#L608) method, because there we can also check that the `client_no_context_takeover` or `server_no_context_takeover` do actually work. So there would be two tests:  
  
**Test 1: Test where the sliding window is not used between writes**  
  
 - start with `Hello` string, role(server or client), `server_no_context_takeover` or `client_no_context_takeover` set to `true`(based on the role)  
 - call `write_some` with that string, the resulted string will be 7 bytes or will have this content: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
 - call `write_some`  with the same string, and the result will be 7 bytes or will have this content: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
  
This is because `server_no_context_takeover` or `client_no_context_takeover` were set to true, so when [this](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/impl/write.hpp#L685) is called, it resets the compression stream, [here](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/detail/impl_base.hpp#L155).  
  
**Test 2: Test where the sliding window is used between writes**  
  
 - start with `Hello` string, role(server or client), `server_no_context_takeover` or `client_no_context_takeover` set to `false`(based on the role)  
 - call `write_some` with that string, the resulted string will be 7 bytes or will have this content: `0xf2 0x48 0xcd 0xc9 0xc9 0x07 0x00`  
 - call `write_some`  with the same string, and the result will be 5 bytes or will have this content: `0xf2 0x00 0x11 0x00 0x00`  
  
This is because `server_no_context_takeover` or `client_no_context_takeover` were set to false, so when [this](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/impl/write.hpp#L685) is called, it won't reset the compression stream, [here](https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/websocket/detail/impl_base.hpp#L155).  
  
Hope this helps a little.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-09-26 23:45:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2318#issuecomment-927395477  

The coverage report is a tiny bit flaky, don't worry too much about it

---
