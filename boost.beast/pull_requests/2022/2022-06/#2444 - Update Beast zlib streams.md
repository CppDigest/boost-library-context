# #2444 Update Beast zlib streams [Merged]

> Username: alandefreitas  
> Created at: 2022-06-02 03:43:09 UTC  
> Updated at: 2022-06-07 17:02:27 UTC  
> Merged at: 2022-06-07 17:02:27 UTC  
> Closed at: 2022-06-07 17:02:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2444  

fix #2439  
  
This PR updates the zlib streams with the changes implemented in zlib from v1.2.11 to v1.2.12.   
  
The most important change is a fix for [CVE-2018-25032](https://github.com/advisories/GHSA-jc36-42cf-vqwj), already mentioned in issue #2439.  
  
zlib fixed this issue https://github.com/madler/zlib/commit/5c44459c3b28a9bd3283aaceab7c615f8020c531, and it was included in v1.2.12, as requested in https://github.com/madler/zlib/issues/605. Thus, this zlib update from v1.2.11 to v1.2.12 is mostly about the CVE.  
  
Although there are no unit tests for the patch in zlib, this PR replicates the tests from https://github.com/zlib-ng/zlib-ng/pull/1208  
  
This reproducers are available from https://www.openwall.com/lists/oss-security/2022/03/26/1 and https://www.openwall.com/lists/oss-security/2022/03/28/1

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-02 04:07:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1144413150  

![pullrequest](https://2444.beast.tracing.cppalliance.org/pullrequest-e4a0bff0.png)  
Timeline tracing charts: [https://2444.beast.tracing.cppalliance.org/index.html](https://2444.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-06-02 04:40:48 UTC  
> Updated_at: 2022-06-03 23:03:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1144429238  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2444](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (925043e) into [develop](https://codecov.io/gh/boostorg/beast/commit/ca824f607ccab6682b955eb91ed49753e6433069?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ca824f6) will **increase** coverage by `0.02%`.  
> The diff coverage is `93.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2444/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2444      +/-   ##  
===========================================  
+ Coverage    94.70%   94.72%   +0.02%       
===========================================  
  Files          152      152                
  Lines        11858    11868      +10       
===========================================  
+ Hits         11230    11242      +12       
+ Misses         628      626       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2444/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RlZmxhdGVfc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2444/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2444/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2444/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.77% <93.33%> (+0.26%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ca824f6...925043e](https://codecov.io/gh/boostorg/beast/pull/2444?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-02 06:01:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1144469651  

No tests?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-06-02 16:25:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1145063834  

> No tests?  
  
Yes. :)  
  
That's why I marked it as a draft for now.  
  
---  
  
I've been considering the tests from https://github.com/zlib-ng/zlib-ng/pull/1208.  
  
That's the best I could find so far, but it includes two text files that are reasonably large: 47Kb and 32Kb. The synthetic data are large because [CVE-2018-25032](https://github.com/advisories/GHSA-jc36-42cf-vqwj) fails precisely when the input has many distant matches.  
  
So we need to discuss how to include these in the tests. We could embed that in the code or we could create a text file as zlib-ng does.   
  
If we decide to use an external test file, I would need some help on how to give this to `beast/include/boost/beast/_experimental/unit_test` as args in the command line and how to make `b2` run that. Adding the tests to Cmake is easier to me, as usual.  
  
What's the best option here?  
  
---  
  
https://github.com/madler/zlib/commit/5c44459c3b28a9bd3283aaceab7c615f8020c531 includes no tests at all. In fact, that's the only line that changed from v1.2.11 to v1.2.12:   
  
https://github.com/madler/zlib/blob/21767c654d31d2dccdde4330529775c6c5fd5389/test/example.c#L443  
  
```c  
    if (err != Z_STREAM_END) {  
```  
  
I couldn't find any equivalent to that in `test/beast/zlib/...`.  
  
Is there anything representing `zlib/example/example.c` in `test/beast/zlib/...`?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-06-02 16:41:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1145079243  

hmmmm there is no way to pass command line args. you would have to make it a separate executable

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-06-02 17:25:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1145116252  

> hmmmm there is no way to pass command line args. you would have to make it a separate executable  
  
Do you mean   
  
- a separate executable without `beast/_experimental/unit_test` so we could accept the text file from the cmd line args   
- **or**  a separate executable using `beast/_experimental/unit_test` with the test data embedded in the binary  
  
?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-06-02 18:49:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1145200010  

You can use `beast/_experimental/unit_test` and provide your own `main`, and either load the file from a command line arg or embed the data. There's nothing magical about `main`:  
https://github.com/boostorg/beast/blob/5799f159c588c3a4b886d0f8d4dd3e0c1fd42bed/include/boost/beast/_experimental/unit_test/main.ipp#L33

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-06-03 19:36:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1146298701  

![pullrequest](https://2444.beast.tracing.cppalliance.org/pullrequest-804f3198.png)  
Timeline tracing charts: [https://2444.beast.tracing.cppalliance.org/index.html](https://2444.beast.tracing.cppalliance.org/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-06-03 20:43:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1146347221  

![pullrequest](https://2444.beast.tracing.cppalliance.org/pullrequest-c19fc523.png)  
Timeline tracing charts: [https://2444.beast.tracing.cppalliance.org/index.html](https://2444.beast.tracing.cppalliance.org/index.html)

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2022-06-03 21:05:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1146361849  

As there are no unit tests for the patch in zlib, this PR replicates the tests from https://github.com/zlib-ng/zlib-ng/pull/1208  
  
These reproducers are available from https://www.openwall.com/lists/oss-security/2022/03/26/1 and https://www.openwall.com/lists/oss-security/2022/03/28/1

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-06-03 21:31:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2444#issuecomment-1146377250  

![pullrequest](https://2444.beast.tracing.cppalliance.org/pullrequest-bc4f9d34.png)  
Timeline tracing charts: [https://2444.beast.tracing.cppalliance.org/index.html](https://2444.beast.tracing.cppalliance.org/index.html)

---
