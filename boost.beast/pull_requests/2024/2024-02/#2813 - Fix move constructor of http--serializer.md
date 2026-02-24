# #2813 Fix move constructor of http::serializer [Closed]

> Username: ashtum  
> Created at: 2024-02-08 13:40:05 UTC  
> Updated at: 2024-02-09 15:51:26 UTC  
> Closed at: 2024-02-09 15:51:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2813  

Minimal reproducible example:  
https://godbolt.org/z/srcnWdrcP  
```C++  
#include <boost/beast.hpp>  
  
using namespace boost::beast::http;  
  
int main()  
{  
  response<empty_body> response;  
  response_serializer<empty_body> serializer{ response };  
  
  boost::system::error_code ec;  
  serializer.next(ec, [](auto, auto) {});  
  
  response_serializer<empty_body> serializer2{ std::move(serializer) };  
}  
  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-08 14:41:35 UTC  
> Updated_at: 2024-02-08 14:59:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2813#issuecomment-1934264462  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`e0a9da2`)](https://app.codecov.io/gh/boostorg/beast/commit/e0a9da265f63daf70d9db61eb20c6b4141c6fead?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 94.68% compared to head [(`22558d9`)](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.04%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2813/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2813      +/-   ##  
===========================================  
- Coverage    94.68%   93.04%   -1.65%       
===========================================  
  Files          154      178      +24       
  Lines        12104    13699    +1595       
===========================================  
+ Hits         11461    12746    +1285       
- Misses         643      953     +310       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5ocHA=) | `90.77% <100.00%> (-1.00%)` | :arrow_down: |  
| [include/boost/beast/http/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `90.90% <ø> (ø)` | |  
  
... and [81 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2813/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e0a9da2...22558d9](https://app.codecov.io/gh/boostorg/beast/pull/2813?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ashtum  
> Created_at: 2024-02-09 15:51:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2813#issuecomment-1936168975  

More tests + address sanitizer, revealed more issues which makes moving serializer after its first use impossible:  
Because the serializer takes ownership of `Field::writer` any reference to internal buffers will be invalidated, like: https://github.com/boostorg/beast/blob/e0a9da265f63daf70d9db61eb20c6b4141c6fead/include/boost/beast/http/impl/fields.hpp#L151  
The same is true for `Body::writer`: https://github.com/boostorg/beast/blob/e0a9da265f63daf70d9db61eb20c6b4141c6fead/include/boost/beast/http/serializer.hpp#L176 they might have internal buffers like `basic_file_body::witer` which any reference to them will be invalidated.

---
