# #1795 Add example for the clean shutdown for an async HTTP server [Closed]

> Username: serhii-muchychka  
> Created at: 2019-12-31 00:05:50 UTC  
> Updated at: 2019-12-31 10:41:30 UTC  
> Closed at: 2019-12-31 10:41:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1795  

I slightly supplemented the example of an async HTTP server  
  
https://github.com/boostorg/beast/issues/1394

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-12-31 00:51:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1795#issuecomment-569838870  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=h1) Report  
> Merging [#1795](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/46ac848fa46ce5b81f2bb5b221774d15c0cdd128?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1795/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1795   +/-   ##  
========================================  
  Coverage    95.17%   95.17%             
========================================  
  Files          156      156             
  Lines        11949    11949             
========================================  
  Hits         11373    11373             
  Misses         576      576  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=footer). Last update [46ac848...12db5cc](https://codecov.io/gh/boostorg/beast/pull/1795?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2019-12-31 01:58:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1795#issuecomment-569847127  

How is this different from the advanced server examples, which already implement shutdown on SIGINT?  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L653

---

## Comment 3

> Username: serhii-muchychka  
> Created_at: 2019-12-31 08:51:55 UTC  
> Updated_at: 2019-12-31 08:55:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1795#issuecomment-569890989  

It supports shutdown via special command from the loopback  
https://github.com/boostorg/beast/blob/12db5ccd0b6cdf39bf2b54cd63aaebd61123d912/example/http/server/clean-shutdown/http_server_clean_shutdown.cpp#L232

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-12-31 09:27:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1795#issuecomment-569896140  

> It supports shutdown via special command from the loopback  
  
There are some problems here. First of all, you are calling `ioc.stop()` which won't wait for the HTTP response to be delivered. The program could exit `main` before the socket is finished sending, giving the peer a partial response or no response.  
  
Second, the `handle_request` function is designed to be the same in every example program. Modifying it to respond specially to a particular URL breaks the design pattern. If you want to do this in your own programs, that's fine - but it is not something that I can support in a Beast example.

---
