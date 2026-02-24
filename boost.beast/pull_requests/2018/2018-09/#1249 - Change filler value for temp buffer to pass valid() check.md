# #1249 Change filler value for temp buffer to pass valid() check [Closed]

> Username: bsergeev  
> Created at: 2018-09-18 20:50:31 UTC  
> Updated at: 2018-09-24 02:45:11 UTC  
> Closed at: 2018-09-24 02:37:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1249  

This is fix for issue https://github.com/boostorg/beast/issues/1245.  
  
The problem happened when read_some buffer ends in the middle of 3-byte symbol (e.g. Bengali). In my example, the first `read_some()` call read 1532 characters, which included the 1st character of the 3-byte sequence, `0xE0`, as the last byte read.  Then, `fail_fast()` was called and filled the last 3 bytes of  `utf8_checker_t::cp_` with `0x81` values, then called `valid()`. Since `{0xE0, 0x81, 0x81, 0x81}` sequence doesn't pass the _overlong_ check for 3-byte values  
```  
(p[0] == 0xe0 && (p[1] & 0xe0) == 0x80) // overlong  
```  
`valid()` returned false and an error was thrown.  
  
Changing the filler values from `0x81` to `0xA0` allows all checks to pass, for 2-, 3-, and probably 4-byte code points (I haven't tested the 4-byte ones, but believe they'll work too).

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-09-18 21:08:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422555526  

Hah, so it was the `utf8_checker` all along

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-09-18 21:08:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422555652  

Do you know if this still passes the Autobahn|Testsuite?

---

## Comment 3

> Username: bsergeev  
> Created_at: 2018-09-18 21:12:44 UTC  
> Updated_at: 2018-09-18 21:12:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422557033  

> Do you know if this still passes the Autobahn|Testsuite?  
  
I don't know how to run them... You might have mentioned how, but I haven't tried that yet.  
Doesn't your CD/CD run these tests?  
  
I've just realized that the problem only happens if the message is pretty large, so that the first `read_some()` doesn't read it all, and the partial read buffer ends after the 1st byte of 3-byte code point, while ending after the 2nd byte works fine.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-09-19 00:09:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422600867  

Okay I ran the Autobahn|Testsuite UTF8 test cases, and they almost all passed. There were two failures. I am linking the successful runs from the previous version of Beast:  
  
https://vinniefalco.github.io/BeastAssets/reports/autobahn/index.html#case_desc_6_4_2  
https://vinniefalco.github.io/BeastAssets/reports/autobahn/index.html#case_desc_6_4_4  
  
These two cases (6.4.2 and 6.4.4) fail with the change to the fill character.  This is the diagnostic output for the failed test cases:  
  
6.4.2 Case Outcome  
Actual events differ from any expected.  
Expected:  
{'NON-STRICT': [('timeout', 'A'), ('timeout', 'B')], 'OK': [('timeout', 'A')]}  
Observed:  
[]  
  
6.4.4 Case Outcome  
Actual events differ from any expected.  
Expected:  
{'NON-STRICT': [('timeout', 'A'), ('timeout', 'B')], 'OK': [('timeout', 'A')]}  
Observed:  
[]  
  
I am attaching the HTML for the two failed cases inside a ZIP archive:  
  
[testcases.zip](https://github.com/boostorg/beast/files/2394912/testcases.zip)

---

## Comment 5

> Username: bsergeev  
> Created_at: 2018-09-19 15:39:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422851273  

Looking into the Autobahn failures...  
  
Travis also reported build failures, but its build output is hard to interpret (I've never dealt with Travis).  
Will take care of the two failing Autobahn tests, then will see what Travis is whining about.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-09-19 17:45:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422895811  

> Travis also reported build failures  
  
Those are not your fault, I wouldn't worry about it.

---

## Comment 7

> Username: bsergeev  
> Created_at: 2018-09-19 19:15:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422925317  

I've added 3 tests to _utf8_checker.cpp_: one to reproduce the problem I've stumbled upon and two for Autobahn tests I've broken (they both failed on 4-byte sequences that I didn't test).  
  
While running _utf8_checker.cpp_, I've noticed some checks that don't make sense to me... For example, why is 224 (0xE0), which is the 1st character in 3-byte sequence, expected to fail:  
https://github.com/boostorg/beast/blob/develop/test/beast/websocket/utf8_checker.cpp#L64  
Shouldn't the beginning of a valid sequence be considered valid?

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2018-09-19 19:19:45 UTC  
> Updated_at: 2018-09-19 19:20:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422926542  

> Shouldn't the beginning of a valid sequence be considered valid?  
  
It is possible that the utf8_checker.cpp tests are wrong. This thing was a pain to write and debug. When you search the Internet for articles about checking for valid utf-8, some of the blog posts contain incorrect code or exposition.

---

## Comment 9

> Username: bsergeev  
> Created_at: 2018-09-19 19:21:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422927008  

Ok, so, Autobahn tests are the ultimate truth?  
How can I run them?

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2018-09-19 19:24:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422927947  

The Beast websocket server example "websocket-server-fast" is designed for the Autobahn tests. Compile it with full optimizations and then run it on some known base port (I use 8080). It will create 3 ports (8080, 8081, and 8082 for example):  
https://github.com/boostorg/beast/blob/f2c3b5dec163ed2b14c5b0e86b63048f1b5a74ff/example/websocket/server/fast/websocket_server_fast.cpp  
  
Then run the Autobahn test suite on those ports. You can run all the tests, or just specific ones. I use this command:  
  
```  
wstest -m fuzzingclient  
```  
  
With a file named "fuzzingclient.json" in the current directory having these contents:  
  
```  
  
{  
   "outdir": "./reports/servers",  
   "servers": [  
      { "url": "ws://127.0.0.1:8080" },  
      { "url": "ws://127.0.0.1:8081" },  
      { "url": "ws://127.0.0.1:8082" }  
   ],  
   "cases": ["6.*"],  
   "exclude-cases": [],  
   "exclude-agent-cases": {}  
}  
```  
The 6.* test cases are all specific to UTF-8

---

## Review 11 [Commented]

> Username: bsergeev  
> Created_at: 2018-09-19 22:25:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1249#pullrequestreview-157046836  

📁 include/boost/beast/websocket/detail/utf8_checker.hpp

```diff
 114 |                 if( (p[1] & 0xc0) != 0x80 ||
 115 |-                     (p[0] & 0xfe) == 0xc0)  // overlong
 115 |+                     (p[0] & 0x1e) == 0)  // overlong
```

> Username: bsergeev  
> Created_at: 2018-09-19 22:25:08 UTC  
> Updated_at: 2018-09-20 19:13:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#discussion_r218983831  

This, and the other overlong checks below, is a minor optimization: since you've already checked that the first 3 bits of `p[0]` are `110`, there's no need to check them here. I'm just checking that the following 4 bits are zero.


---

## Review 12 [Commented]

> Username: bsergeev  
> Created_at: 2018-09-19 22:29:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1249#pullrequestreview-157047833  

📁 include/boost/beast/websocket/detail/utf8_checker.hpp

```diff
 125 |-                     || (p[0] == 0xed && (p[1] & 0xe0) == 0xa0) // surrogate
 124 |+                     || (p[0] == 0xe0 && (p[1] & 0x20) == 0) // overlong
 125 |+                     || (p[0] == 0xed && (p[1] & 0x3C) >= 0x3C) // surrogate
```

> Username: bsergeev  
> Created_at: 2018-09-19 22:29:17 UTC  
> Updated_at: 2018-09-20 19:13:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#discussion_r218984652  

The new surrogate check is sub-optimal and will be fixed in the next commit...


---

## Comment 13

> Username: bsergeev  
> Created_at: 2018-09-19 23:21:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-422989717  

The latest commit fixed previously failing Autobahn tests, but broke others.  
Now, as I can run these tests, I will make sure that they all work.  
Fingers crossed, will finish this tomorrow!

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2018-09-20 00:46:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423003143  

Well these tests you wrote look very nice! I'm glad you understand what is going on with the code points, because I sure don't. Do you have a link to a good reference that clearly explains the process for determining if a code point is valid? If yes then you could include it as a comment in the source file.

---

## Comment 15

> Username: bsergeev  
> Created_at: 2018-09-20 05:52:06 UTC  
> Updated_at: 2018-09-20 15:03:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423048490  

**All Autobahn 6 tests are succeeding!**  
  
I had to edit a couple of places in the unit test, as the first byte `0xF0` alone is not a failure, as it can start valid 4-byte sequences. It can become a problem, only if the following byte has zeros in last 6 bits, leading to overlong. But this condition is checked separately, so `0xF0` alone should not be deemed an invalid byte.  
  
I've worked with UTF-8 before, so, it wasn't totally new to me. To refresh my memory, I read only Wikipedia, https://en.wikipedia.org/wiki/UTF-8, which has sufficient information, but perhaps not well structured.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2018-09-20 16:54:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423256198  

Actually the Travis failures are due to a compilation error:  
https://travis-ci.org/boostorg/beast/jobs/430871487#L1431

---

## Comment 17

> Username: bsergeev  
> Created_at: 2018-09-20 17:23:57 UTC  
> Updated_at: 2018-09-20 17:24:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423265841  

>  Travis failures are due to a compilation error  
  
Silly me--I used some temporary code and forgot to clean it, after merging the tests...  
Will fix that.

---

## Comment 18

> Username: codecov[bot]  
> Created_at: 2018-09-20 23:47:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423369654  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=h1) Report  
> Merging [#1249](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f2c3b5dec163ed2b14c5b0e86b63048f1b5a74ff?src=pr&el=desc) will **decrease** coverage by `0.06%`.  
> The diff coverage is `78.26%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1249/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1249      +/-   ##  
===========================================  
- Coverage    95.52%   95.45%   -0.07%       
===========================================  
  Files          113      113                
  Lines        11064    11096      +32       
===========================================  
+ Hits         10569    10592      +23       
- Misses         495      504       +9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/boostorg/beast/pull/1249/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `93.86% <78.26%> (-5.38%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=footer). Last update [f2c3b5d...7481666](https://codecov.io/gh/boostorg/beast/pull/1249?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 19

> Username: vinniefalco  
> Created_at: 2018-09-24 02:38:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423867208  

Great job on this Boris, and thank you very much for taking care of it!

---

## Comment 20

> Username: bsergeev  
> Created_at: 2018-09-24 02:40:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423867357  

My pleasure! Thank you for the great library!  
  
Are you in coming to Registration Reception in 20 minutes?  
  
On Sun, Sep 23, 2018 at 7:38 PM Vinnie Falco <notifications@github.com>  
wrote:  
  
> Great job on this Boris, and thank you very much for taking care of it!  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/1249#issuecomment-423867208>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ACprjWrpcHFAnxniFdJIric1Cq4ud2dMks5ueEWcgaJpZM4WuuP2>  
> .  
>

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2018-09-24 02:42:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423867453  

I'm coming to the Speaker's dinner on Thursday night and then giving my talk Friday afternoon :)

---

## Comment 22

> Username: bsergeev  
> Created_at: 2018-09-24 02:45:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1249#issuecomment-423867658  

See you then!  
  
On Sun, Sep 23, 2018 at 7:42 PM Vinnie Falco <notifications@github.com>  
wrote:  
  
> I'm coming to the Speaker's dinner on Thursday night and then giving my  
> talk Friday afternoon :)  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/1249#issuecomment-423867453>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ACprjUiepDcZGiYCW2bjaHnBXFdjXYupks5ueEaJgaJpZM4WuuP2>  
> .  
>

---
