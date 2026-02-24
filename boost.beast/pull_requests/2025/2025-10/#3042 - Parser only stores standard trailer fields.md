# #3042 Parser only stores standard trailer fields [Merged]

> Username: ashtum  
> Created at: 2025-10-09 09:05:46 UTC  
> Updated at: 2025-10-16 18:28:39 UTC  
> Merged at: 2025-10-16 12:02:39 UTC  
> Closed at: 2025-10-16 12:02:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3042  



---

## Comment 1

> Username: sehe  
> Created_at: 2025-10-09 12:41:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3385686225  

Is there a linked issue for this change?  
  
Specifically, I am concerned this will break custom trailer headers for existing users of Beast, see e.g. https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Trailer.  
  
As you can see it's not a feature browsers typically support, but we're typically not writing/dealing with browsers here, so the feature might be used (for things like debugging, telemetry, custom digests).

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-10-09 13:17:55 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3385836905  

> Is there a linked issue for this change?  
>  
  
This is actually a security concern and stems from the fact that we are storing trailer fields and initial fields in the same container. As a result, it is possible for a chunked message to add arbitrary fields to a message at the end.  
  
> Specifically, I am concerned this will break custom trailer headers for existing users of Beast, see e.g. https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Trailer.  
>   
> As you can see it's not a feature browsers typically support, but we're typically not writing/dealing with browsers here, so the feature might be used (for things like debugging, telemetry, custom digests).  
  
For more advanced use cases, users can create their own parser class derived from `basic_parser`. Another alternative could be to add a virtual function to the `parser` class (not `basic_parser`) that determines whether a trailer field should be stored, with a default implementation that allows only the standard ones. However, considering how rarely trailer headers are used (if used at all), I think we don't need to make this customization easy.  
  
By the way, this is a draft PR, so we can consider all the alternatives and decide.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-10-09 20:02:45 UTC  
> Updated_at: 2025-10-16 18:28:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3387320012  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3042?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.18%. Comparing base ([`72dc439`](https://app.codecov.io/gh/boostorg/beast/commit/72dc439584ef3cdf7a75dfae5cf085c259acd654?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`a92c5ce`](https://app.codecov.io/gh/boostorg/beast/commit/a92c5cebd8a149a81ff40be76ae72e4a59006d4f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3042/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3042?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #3042   +/-   ##  
========================================  
  Coverage    93.17%   93.18%             
========================================  
  Files          176      176             
  Lines        13689    13690    +1       
========================================  
+ Hits         12755    12757    +2       
+ Misses         934      933    -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3042?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3042?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <ø> (ø)` | |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3042?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fbasic_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `97.14% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/beast/http/impl/field.ipp](https://app.codecov.io/gh/boostorg/beast/pull/3042?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Ffield.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `98.59% <100.00%> (+0.94%)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3042?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `89.02% <100.00%> (+2.06%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3042?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3042?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [72dc439...a92c5ce](https://app.codecov.io/gh/boostorg/beast/pull/3042?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: ashtum  
> Created_at: 2025-10-10 14:29:55 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3390479104  

Another option could be to check if a trailer field is listed in the `Trailer` field and then store it. This approach assumes that the user checks the `Trailer` field when the headers are received, so it would not be safe by default, which is not ideal and does not change the status quo.

---

## Comment 5

> Username: sehe  
> Created_at: 2025-10-10 22:59:40 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3392528671  

> This approach assumes that the user checks the Trailer field when the headers are received, so it would not be safe by default, which is not ideal and does not change the status quo.  
  
I think it's how the RFC is intended, though. Also, it would allow you to check that disallowed headers are not named, or no trailing headers would overwrite existing headers. That way, it would already safer by default.  
  
I can see room for a flag "allow non-standard trailers". I'm not sure how feasible it would be for a derived parser to override the on-trailer-header handler, but that idea could work.

---

## Comment 6

> Username: ashtum  
> Created_at: 2025-10-11 08:37:41 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3393068237  

> Also, it would allow you to check that disallowed headers are not named  
  
The problem is there isn’t a concrete list of disallowed headers, that’s why I went with a whitelist instead.  
  
> or no trailing headers would overwrite existing headers. That way, it would already safer by default.  
  
Yes, this would make it much safer, although it would still allow an attacker to append arbitrary fields (e.g. `X-Forwarded-For`).  
  
> I can see room for a flag "allow non-standard trailers". I'm not sure how feasible it would be for a derived parser to override the on-trailer-header handler, but that idea could work.  
  
This depends on how often trailer headers are used in the wild, and if they are, how many of these usages don’t fall into the whitelist we already have. Considering trailer fields are disallowed in browsers (except `Server-Timing` in Firefox) and most HTTP libraries, I don’t expect any usage in the wild (this is just an assumption though). Unless there are cases that rely on libraries for both client and server sides that allows arbitrary trailer fields. In that case, users can implement their own parser from `basic_parser` (starting by copying the existing `parser` implementation in Beast).  
  
So, my attempt is to avoid complicating the current interface unless we have evidence that the feature is used in the wild. The closest thing I found was gRPC’s `grpc-status` and `grpc-message`, but gRPC is built on top of HTTP/2.

---

## Comment 7

> Username: sehe  
> Created_at: 2025-10-12 11:22:27 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3394194049  

> > Also, it would allow you to check that disallowed headers are not named  
>   
> The problem is there isn’t a concrete list of disallowed headers, that’s why I went with a whitelist instead.  
  
Honoring the `Trailer` header *is* a whitelist. My point was that you can _"check that disallowed headers are not named"_ (in the Trailer header)

---

## Comment 8

> Username: sehe  
> Created_at: 2025-10-12 11:33:02 UTC  
> Updated_at: 2025-10-12 11:33:16 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3394212031  

> > or no trailing headers would overwrite existing headers. That way, it would already safer by default.  
>   
> Yes, this would make it much safer, although it would still allow an attacker to append arbitrary fields (e.g. `X-Forwarded-For`).  
  
Only iff it were named in a `Trailer` header value. This will vastly reduce the opportunity for abuse in most server architectures.  
  
  
  
>   >  I can see room for a flag "allow non-standard trailers". I'm not sure how feasible it would be for a derived parser to override the on-trailer-header handler, but that idea could work.  
>   
> This depends on how often trailer headers are used in the wild, and if they are, how many of these usages don’t fall into the whitelist we already have.  
  
Again, my concern is the opposite. Currently, users of the library can rely on this functionality. It *should* be reasonably possible to achieve the same result with the changes. Again:  
  
> I'm not sure how feasible it would be for a derived parser to override the on-trailer-header handler, but that idea could work.  
  
If it's trivial, a 16-word sketch in the release notes could do

---

## Comment 9

> Username: ashtum  
> Created_at: 2025-10-14 14:16:38 UTC  
> Updated_at: 2025-10-15 09:19:35 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3402111618  

@sehe, the behavior has changed as follows:  
  
- By default, only standard trailer headers that are listed in the `Trailer` field are merged.  
- When `parser::merge_all_trailers(true)` is set, all trailers listed in the `Trailer` field are merged.  
  
Regardless of the settings, any trailer header not listed in the `Trailer` field is always discarded.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2025-10-15 08:55:12 UTC  
> Updated_at: 2025-10-16 12:04:58 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3405300208  

An automated preview of the documentation is available at [https://3042.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://3042.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-16 12:04:57 UTC

---

## Comment 11

> Username: sehe  
> Created_at: 2025-10-15 13:27:48 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3406454435  

> @sehe, the behavior has changed as follows:  
  
Awesome. That looks like it covers all purposes nicely.   
  
My only review comment would be that the comment I'm replying to does a FAR better job documenting the behavior than the doxygen. Notably, `on_trailer_field_impl` docs don't mention any conditions.   
  
`merge_all_trailers` could be better named (it *never* merges all trailers, after all). Perhaps `merge_non_standard_tailers`? Or slightly less clunky when inverted: `merge_standard_trailers_only`?

---

## Comment 12

> Username: ashtum  
> Created_at: 2025-10-15 13:56:20 UTC  
> Updated_at: 2025-10-15 16:19:38 UTC  
> Url: https://github.com/boostorg/beast/pull/3042#issuecomment-3406576009  

> My only review comment would be that the comment I'm replying to does a FAR better job documenting the behavior than the doxygen. Notably, `on_trailer_field_impl` docs don't mention any conditions.  
  
`basic_parser` calls `on_trailer_field_impl` unconditionally, it is the `parser` class that does the filtering. so you can find related docs here:  
[http::parser::merge_all_trailers (2 of 2 overloads)](https://3042.beast.prtest.cppalliance.org/libs/beast/doc/html/beast/ref/boost__beast__http__parser/merge_all_trailers/overload2.html)  
  
> `merge_all_trailers` could be better named (it _never_ merges all trailers, after all). Perhaps `merge_non_standard_tailers`? Or slightly less clunky when inverted: `merge_standard_trailers_only`?  
  
I first went with `merge_nonstandard_trailers` but it's not correct either, because there's not an standard whitelist either, it is more like a well-know list or something.

---
