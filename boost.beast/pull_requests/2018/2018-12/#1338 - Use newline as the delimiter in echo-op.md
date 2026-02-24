# #1338 Use newline as the delimiter in echo-op [Closed]

> Username: octobanana  
> Created at: 2018-12-03 02:22:30 UTC  
> Updated at: 2018-12-03 22:37:23 UTC  
> Closed at: 2018-12-03 04:25:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1338  



---

## Comment 1

> Username: octobanana  
> Created_at: 2018-12-03 02:22:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1338#issuecomment-443569268  

Currently, the __echo-op__ example uses the carriage return `\r` delimiter for `async_read_until`. Running the server and sending a message to it with curl or netcat:  
```sh  
printf 'Hello, World!\r\n' | nc 127.0.0.1 8080  
```  
leads to some potentially confusing behaviour. The `async_read_until` function reads up to and including the first `\r`. When the message is received by the client and printed to stdout, the trailing `\r` ends up moving the cursor back to the start of the line, resulting in the prompt overwriting the message. It appears as if nothing has happened.  
  
If `async_read_until` uses the newline `\n` as the delimiter, the echoed output will not be overwritten, regardless of whether the line ends in `\n` or `\r\n`.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-12-03 04:26:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1338#issuecomment-443585400  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=h1) Report  
> Merging [#1338](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/a5739c3ea0526ecf960a19491cac3caa74667137?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1338/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1338   +/-   ##  
========================================  
  Coverage    92.05%   92.05%             
========================================  
  Files          124      124             
  Lines        11536    11536             
========================================  
  Hits         10620    10620             
  Misses         916      916  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=footer). Last update [a5739c3...00cc7eb](https://codecov.io/gh/boostorg/beast/pull/1338?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
