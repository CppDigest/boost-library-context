# #2443 Fix large files transfer on Windows [Merged]

> Username: petrepircalabu  
> Created at: 2022-06-01 00:08:41 UTC  
> Updated at: 2022-06-21 23:37:44 UTC  
> Merged at: 2022-06-21 23:37:44 UTC  
> Closed at: 2022-06-21 23:37:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2443  

The maximum number of bytes to write using TransmitFile cannot be  
greater than INT_MAX - 1, otherwise the function fails with WSAEINVAL.  
  
https://docs.microsoft.com/en-us/windows/win32/api/mswsock/nf-mswsock-transmitfile  
  
The issue can be reproduced using the http-server-sync example, by  
sending a GET request for a file larger than INTMAX.  
  
e.g:  
$ curl -v http://127.0.0.1:8080/ubuntu.iso -o ubuntu.iso  
*   Trying 127.0.0.1:8080...  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
                                 Dload  Upload   Total   Spent    Left  Speed  
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Connected to 127.0.0.1 (127.0.0.1) port 8080 (#0)  
> GET /ubuntu.iso HTTP/1.1  
> Host: 127.0.0.1:8080  
> User-Agent: curl/7.83.0  
> Accept: */*  
>  
* Mark bundle as not supporting multiuse  
< HTTP/1.1 200 OK  
< Server: Boost.Beast/330  
< Content-Type: application/text  
< Content-Length: 3654957056  
<  
{ [0 bytes data]  
* transfer closed with 3654957056 bytes remaining to read  
  0 3485M    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  
* Closing connection 0  
curl: (18) transfer closed with 3654957056 bytes remaining to read  
  
Signed-off-by: Petre Pircalabu <ppircalabu@bitdefender.com>

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-06-01 00:12:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2443#issuecomment-1142822833  

oh, a nice easy fix :)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-01 00:32:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2443#issuecomment-1142868012  

![pullrequest](https://2443.beast.tracing.cppalliance.org/pullrequest-8db5f06c.png)  
Timeline tracing charts: [https://2443.beast.tracing.cppalliance.org/index.html](https://2443.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-01 00:49:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2443#issuecomment-1142909169  

![pullrequest](https://2443.beast.tracing.cppalliance.org/pullrequest-23f289b2.png)  
Timeline tracing charts: [https://2443.beast.tracing.cppalliance.org/index.html](https://2443.beast.tracing.cppalliance.org/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-06-01 01:15:11 UTC  
> Updated_at: 2022-06-01 01:16:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2443#issuecomment-1142967709  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2443](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1dde0c2) into [develop](https://codecov.io/gh/boostorg/beast/commit/5799f159c588c3a4b886d0f8d4dd3e0c1fd42bed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5799f15) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2443/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2443      +/-   ##  
===========================================  
- Coverage    94.72%   94.69%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11858    11858                
===========================================  
- Hits         11233    11229       -4       
- Misses         625      629       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2443/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5799f15...1dde0c2](https://codecov.io/gh/boostorg/beast/pull/2443?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-06-21 23:35:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2443#issuecomment-1162462506  

Good

---
