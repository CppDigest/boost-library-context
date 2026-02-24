# #935 Enable body composition [Closed]

> Username: djarek  
> Created at: 2017-12-10 09:37:40 UTC  
> Updated at: 2017-12-16 10:03:30 UTC  
> Closed at: 2017-12-16 10:03:23 UTC  
> Url: https://github.com/boostorg/beast/pull/935  

This change enables composing bodies into multiple layers e.g. compressed_body<json_body<T>>. Resolves #884.  
  
Actions required:  
- change body writer constructor signature to  
	writer(header<isRequest, Fields> const&, value_type const&)  
- change body reader constructor signature to  
	reader(header<isRequest, Fields>&, value_type&)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-10 09:57:18 UTC  
> Updated_at: 2017-12-14 20:50:17 UTC  
> Url: https://github.com/boostorg/beast/pull/935#issuecomment-350537009  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=h1) Report  
> Merging [#935](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3edb30b2e8d64615fc8324c46d5485015bd4ab85?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/935/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #935      +/-   ##  
===========================================  
+ Coverage    95.67%   95.69%   +0.01%       
===========================================  
  Files          104      104                
  Lines        10424    10441      +17       
===========================================  
+ Hits          9973     9991      +18       
+ Misses         451      450       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `83.01% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/empty\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `72.72% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/parser.ipp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.71% <100%> (+0.15%)` | :arrow_up: |  
| [include/boost/beast/http/span\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NwYW5fYm9keS5ocHA=) | `93.93% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_dynamic\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2R5bmFtaWNfYm9keS5ocHA=) | `85.29% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `80.48% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.96% <100%> (ø)` | :arrow_up: |  
| ... and [2 more](https://codecov.io/gh/boostorg/beast/pull/935/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=footer). Last update [3edb30b...18e2132](https://codecov.io/gh/boostorg/beast/pull/935?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-12-12 00:38:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/935#pullrequestreview-82688433  

📁 test/beast/http/serializer.cpp

```diff
 153 |+     {
 154 |+         response<deprecated_body> resp{};
 155 |+         request<deprecated_body> req{};
```

> Username: vinniefalco  
> Created_at: 2017-12-12 00:38:13 UTC  
> Updated_at: 2017-12-14 20:32:24 UTC  
> Url: https://github.com/boostorg/beast/pull/935#discussion_r156243107  

Why are you value-initializing?

> Username: djarek  
> Created_at: 2017-12-12 20:46:42 UTC  
> Updated_at: 2017-12-14 20:32:24 UTC  
> Url: https://github.com/boostorg/beast/pull/935#discussion_r156490309  

Probably a copy-paste leftover. Will fix.


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-12-12 20:56:50 UTC  
> Url: https://github.com/boostorg/beast/pull/935#issuecomment-351191625  

Please rebase against my **v150** and make the following changes:  
If `BOOST_BEAST_ALLOW_DEPRECATED` is non zero (e.g. `#if BOOST_BEAST_ALLOW_DEPRECATED`) then allow the deprecated constructors. Otherwise, cause a `static_assert` if those constructors are instantiated. You will have to do some tricks to make the assert dependent on template parameter. For example `static_assert(sizeof(Body)==0, "message")`. Have a look at how I did it in the **v150** branch and ask questions if needed.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-12-13 23:51:10 UTC  
> Updated_at: 2017-12-13 23:51:43 UTC  
> Url: https://github.com/boostorg/beast/pull/935#issuecomment-351562903  

The commit message should indicate the change (new constructor signatures), not the end result (enable body composition). For example:  
  
```  
New BodyReader and BodyWriter constructors (API Change):  
  
fix #884  
  
* BodyReader and BodyWriter constructors now require the header and  
  body elements to be passed as distinct header and value_type objects.  
  
This enables the composition of body types, for example:  
  
    http::response<compressed_body<http::string_body>> res;  
  
* The previous single-argument constructors are deprecated and will be  
  removed in a subsequent version.  
  
Actions Required:  
  
* Change user-defined instances of BodyReader or BodyWriter constructor  
  signatures to the two-argument form  
  
OR  
  
* Define the macro BOOST_BEAST_ALLOW_DEPRECATED in the project  
```

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2017-12-14 23:32:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/935#pullrequestreview-83678946  

📁 include/boost/beast/http/serializer.hpp

```diff
  64 |         "BodyWriter requirements not met");
  65 |- 
  65 |+     using body_value_type = typename Body::value_type;
```

> Username: vinniefalco  
> Created_at: 2017-12-14 23:32:42 UTC  
> Url: https://github.com/boostorg/beast/pull/935#discussion_r157090767  

tsk tsk... you can't just casually add a public nested type! This affects the public API. As library developers we have to prefer to be inconvenienced over adding unnecessary interfaces.


---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-12-14 23:55:32 UTC  
> Url: https://github.com/boostorg/beast/pull/935#issuecomment-351872271  

I've merged this into **v150**: https://github.com/vinniefalco/beast/commits/v150  
  
All of my edits are in one commit: https://github.com/vinniefalco/beast/commit/9842af966dc9843e794e159cd580f06672ecd50a  
  
The commits marked "[FOLD]" will be folded into the previous commit

---
