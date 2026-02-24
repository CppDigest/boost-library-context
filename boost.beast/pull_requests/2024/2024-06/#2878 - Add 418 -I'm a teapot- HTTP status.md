# #2878 Add 418 "I'm a teapot" HTTP status [Merged]

> Username: alex65536  
> Created at: 2024-06-02 11:39:44 UTC  
> Updated at: 2024-06-04 08:56:41 UTC  
> Merged at: 2024-06-03 14:29:18 UTC  
> Closed at: 2024-06-03 14:29:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2878  

Though this HTTP status is just a joke, it is supported by numerous HTTP implementations, including (but not limited to):  
  
* [Python](https://github.com/python/cpython/blob/f79ffc879b919604ed5de22ece83825006cf9a17/Lib/http/__init__.py#L130)  
* [POCO](https://github.com/pocoproject/poco/blob/bc29f30e5cf035e860b3d2c1fc5df570f01513ea/Net/include/Poco/Net/HTTPResponse.h#L90)  
* [Golang](https://pkg.go.dev/net/http#StatusTeapot)  
* [Rust `actix-web` crate](https://docs.rs/actix-web/4.6.0/actix_web/http/struct.StatusCode.html#associatedconstant.IM_A_TEAPOT)  
  
So, I think it's also a good idea to support this HTTP status code in Beast.  
  
Also, C++ is occasionally used for embedded programming, such as smart teapots, and I think it also justifies adding this status code :) :)

---

## Comment 1

> Username: ashtum  
> Created_at: 2024-06-02 12:26:29 UTC  
> Updated_at: 2024-06-02 12:28:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143828078  

Thanks for the PR. We have recently updated the status code list to conform with the boring IANA registry (https://github.com/boostorg/beast/pull/2811/commits/180d9977011203fe53128441638c64528f1c67f8). So, I'm not sure if it's a good idea to break it.

---

## Comment 2

> Username: alex65536  
> Created_at: 2024-06-02 12:32:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143830227  

> I'm not sure if it's a good idea to break it.  
  
Well, I think that having more status codes is better than having less of them. Also, status code 418 is reserved, so it's quite unlikely that it will receive any other meaning:  
  
> Therefore, the 418 status code is reserved in the IANA HTTP Status Code Registry. This indicates that the status code cannot be assigned to other applications currently.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-06-02 12:40:41 UTC  
> Updated_at: 2024-06-02 12:58:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143833779  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2878?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.23%. Comparing base [(`146de67`)](https://app.codecov.io/gh/boostorg/beast/commit/146de67f5e9238e6159a6de7e20e8722df38a581?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`490e9df`)](https://app.codecov.io/gh/boostorg/beast/commit/490e9dfb4d7721913d779e6bcd063acddd6adb2d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2878/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2878?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2878   +/-   ##  
========================================  
  Coverage    93.23%   93.23%             
========================================  
  Files          177      177             
  Lines        13670    13671    +1       
========================================  
+ Hits         12745    12746    +1       
  Misses         925      925             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2878?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/status.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2878?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fstatus.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `94.31% <100.00%> (+0.06%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2878?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2878?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [146de67...490e9df](https://app.codecov.io/gh/boostorg/beast/pull/2878?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: ashtum  
> Created_at: 2024-06-02 12:43:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143834909  

> [[RFC2324](https://www.rfc-editor.org/rfc/rfc9110.html#RFC2324)] was an April 1 RFC that lampooned the various ways HTTP was abused; one such abuse was the definition of an application-specific 418 status code, which has been deployed as a joke often enough for the code to be unusable for any future use.  
> Therefore, the 418 status code is reserved in the IANA HTTP Status Code Registry. This indicates that the status code cannot be assigned to other applications currently. If future circumstances require its use (e.g., exhaustion of 4NN status codes), it can be re-assigned to another use.  
  
I guess we can add it then :)  
  
> Well, I think that having more status codes is better than having less of them  
  
Users can use the corresponding integral value for the status codes that are not in the list. The problem with supporting non-standard status codes is that they overlap, and we can't add all of them.

---

## Comment 5

> Username: alex65536  
> Created_at: 2024-06-02 12:50:44 UTC  
> Updated_at: 2024-06-02 12:51:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143840825  

> The problem with supporting non-standard status codes is that they overlap, and we can't add all of them.  
  
Well, given that this code is reserved to prevent it from being reused, the chance of overlap is highly unlikely.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2024-06-02 13:07:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143846673  

> I think that having more status codes is better than having less of them.  
  
Interesting, because I think the exact opposite. Fewer choices means less confusion, especially if the extra choices add no value.

---

## Comment 7

> Username: ashtum  
> Created_at: 2024-06-02 13:07:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2878#issuecomment-2143847013  

> Well, given that this code is reserved to prevent it from being reused, the chance of overlap is highly unlikely.  
  
I didn't mean 418; I was referring to the reason why we don't add non-standard status codes in general, like the ones Nginx uses, for example.

---
