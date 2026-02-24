# #2861 fix asserts, it's possible that we reach code only with 3 symbols [Merged]

> Username: ruz  
> Created at: 2024-05-08 10:05:45 UTC  
> Updated at: 2024-05-11 14:48:59 UTC  
> Merged at: 2024-05-10 13:58:20 UTC  
> Closed at: 2024-05-10 13:58:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2861  

This happens when:  
* chunked encoding  
* body is empty  
* chunk has no extensions  
* and put calls are really weird

---

## Comment 1

> Username: ashtum  
> Created_at: 2024-05-08 11:35:36 UTC  
> Updated_at: 2024-05-08 12:10:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2100376121  

The minimum valid chunked body is `0\r\n\r\n`. Do you want to consider `0\r\n` as a valid body?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-05-08 12:14:46 UTC  
> Updated_at: 2024-05-09 16:04:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2100442186  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 92.98%. Comparing base [(`98b8be4`)](https://app.codecov.io/gh/boostorg/beast/commit/98b8be489fa7a74753a724d8d0772d90bcbed0fc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9dcd15a`)](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2861/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2861?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2861      +/-   ##  
===========================================  
- Coverage    93.06%   92.98%   -0.09%       
===========================================  
  Files          178      178                
  Lines        13687    13651      -36       
===========================================  
- Hits         12738    12693      -45       
- Misses         949      958       +9       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2861?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fbasic_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.45% <100.00%> (+<0.01%)` | :arrow_up: |  
  
... and [24 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2861/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [98b8be4...9dcd15a](https://app.codecov.io/gh/boostorg/beast/pull/2861?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: ruz  
> Created_at: 2024-05-09 09:24:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2102289908  

No, I don't want to. This is a streamed parser, imagine put calls with the following sequence: '0\r\n', '\r' and '\n'. The current code will fail with the assert on the second put as n will be equal to 4.  
  
What I want to do is to correct the assert and make it valid. If flagExpectCRLF is set then n should be >= 5, otherwise 3 is possible and ok. Code that goes after, searches for CRLFCRLF (eom) sequence and correctly returns need_more.

---

## Comment 4

> Username: ashtum  
> Created_at: 2024-05-09 10:39:27 UTC  
> Updated_at: 2024-05-09 13:09:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2102413206  

> What I want to do is to correct the assert and make it valid. If flagExpectCRLF is set then n should be >= 5, otherwise 3 is possible and ok. Code that goes after, searches for CRLFCRLF (eom) sequence and correctly returns need_more.  
  
You are correct; the current code returns the error_code `error::need_more`, which expects more data in the next call, which can be as little as one byte. Thank you very much for reporting this and for your PR; I'll add the related test cases and merge this.  
  
This is actually reproducible with the following code:  
https://godbolt.org/z/sfdjKez3q  
```C++  
#include <boost/beast.hpp>  
#include <boost/beast/_experimental/test/stream.hpp>  
  
using namespace boost::beast;  
  
int main()  
{  
  net::io_context ioc;  
  test::stream s{ioc};  
  http::response_parser<http::string_body> pr{};  
  
  s.append("HTTP/1.1 200 OK\r\n"  
           "Transfer-Encoding: chunked\r\n"  
           "\r\n"  
           "0"  
           "\r\n");  
  
  flat_buffer buf;  
  http::read_some(s, buf, pr);  
  
  s.append("\r");  
  // s.append("\r\n"); // works fine  
  http::read_some(s, buf, pr); // assert fires  
}  
```  
  
@vinniefalco

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2024-05-10 12:31:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2104527425  

LGTM

---

## Comment 6

> Username: ruz  
> Created_at: 2024-05-11 14:48:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2861#issuecomment-2105913237  

thanks

---
