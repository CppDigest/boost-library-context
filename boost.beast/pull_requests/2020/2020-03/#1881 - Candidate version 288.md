# #1881 Candidate version 288 [Merged]

> Username: madmongo1  
> Created at: 2020-03-18 20:10:21 UTC  
> Updated at: 2020-03-21 16:20:03 UTC  
> Merged at: 2020-03-21 15:16:43 UTC  
> Closed at: 2020-03-21 15:16:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1881  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 20:21:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377206316  

📁 include/boost/beast/http/impl/basic_parser.hpp

```diff
  63 |+ {
  64 |+     // don't pessimise possible NRVO
  65 |+     boost::optional<std::uint64_t> result;
```

> Username: vinniefalco  
> Created_at: 2020-03-18 20:21:45 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394616301  

What is this nonsense of NRVO? `boost::optional<std::uint64_t>` is a trivial type...


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 21:43:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377256758  

📁 test/beast/http/parser.cpp

```diff
 443 |+                 "Access-Control-Allow-Origin: *\r\n"
 444 |+                 "Access-Control-Allow-Methods: GET, OPTIONS, POST\r\n"
 445 |+                 "Access-Control-Allow-Headers: Authorization, Content-Type, Accept\r\n"
```

> Username: vinniefalco  
> Created_at: 2020-03-18 21:43:41 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394656857  

Why is it necessary to have all these extra fields like `Access-Control-Allow-Origin`? Do you think it makes the test better?

> Username: madmongo1  
> Created_at: 2020-03-18 22:36:07 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394678755  

This code is copy/pasted from the MCVE we were given when the bug was reported.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 21:43:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377256933  

📁 test/beast/http/parser.cpp

```diff
 379 |+                 "\r\n";
 380 |+ 
 381 |+             boost::beast::http::response_parser<boost::beast::http::string_body> parser;
```

> Username: vinniefalco  
> Created_at: 2020-03-18 21:43:59 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394656992  

Why are all these names fully qualified??

> Username: madmongo1  
> Created_at: 2020-03-18 22:36:13 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394678785  

This code is copy/pasted from the MCVE we were given when the bug was reported.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 21:44:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377257188  

📁 test/beast/http/parser.cpp

```diff
 381 |+             boost::beast::http::response_parser<boost::beast::http::string_body> parser;
 382 |+             boost::beast::error_code ec;
 383 |+             parser.put(boost::asio::buffer(header, sizeof(header) - 1), ec);
```

> Username: vinniefalco  
> Created_at: 2020-03-18 21:44:24 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394657184  

Why aren't you using `string_view header` instead of `char const[]`?

> Username: madmongo1  
> Created_at: 2020-03-18 22:36:34 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394678895  

This code is copy/pasted from the MCVE we were given when the bug was reported.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 22:24:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377277614  

📁 include/boost/beast/http/impl/basic_parser.hpp

```diff
  60 |+ boost::optional<std::uint64_t>
  61 |+ basic_parser<isRequest>::
  62 |+ impl_content_length() const
```

> Username: vinniefalco  
> Created_at: 2020-03-18 22:24:38 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394673845  

How is this different from the already existing function `basic_parser::content_length`?

> Username: madmongo1  
> Created_at: 2020-03-18 22:37:36 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394679256  

The public function cannot be called during parsing because the assert will fail. So I have split it up into a public function which asserts the precondition and a private one that does not.

> Username: vinniefalco  
> Created_at: 2020-03-19 00:11:43 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394709727  

The problem is that users can subclass `basic_parser`, so you have given `parser` a capability that ordinary users do not have access to (since `impl_content_length` is private).

> Username: madmongo1  
> Created_at: 2020-03-19 06:46:44 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394819219  

The capabilities and behaviour of the public API have not changed at all.  
I have merely refactored the public function’s implementation to be expressed in terms of a private function which is now used in the parsing of headers (where calling the public API would assert)


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 22:24:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377277712  

📁 include/boost/beast/http/impl/basic_parser.ipp

```diff
  54 |-         return boost::none;
  55 |-     return len0_;
  53 |+     return impl_content_length();
```

> Username: vinniefalco  
> Created_at: 2020-03-18 22:24:53 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394673918  

Why?

> Username: madmongo1  
> Created_at: 2020-03-18 22:37:46 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394679333  

as above


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 22:26:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377279050  

📁 include/boost/beast/http/impl/basic_parser.ipp

```diff
 815 |+             }
 816 |+             else
 817 |+                 existing = v;
```

> Username: vinniefalco  
> Created_at: 2020-03-18 22:26:27 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394674526  

nit: if the `if` has braces then the `else` needs them too

> Username: madmongo1  
> Created_at: 2020-03-18 22:38:01 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394679418  

ok


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 22:26:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377279799  

📁 test/beast/http/basic_parser.cpp

```diff
 262 |+             if(!BEAST_EXPECTS(ec == result, ec.message()))
 263 |+             {
 264 |+                 std::abort();
```

> Username: vinniefalco  
> Created_at: 2020-03-18 22:26:54 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394674691  

`abort`?

> Username: madmongo1  
> Created_at: 2020-03-18 22:38:18 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394679507  

i had to have something to put a breakpoint on

> Username: vinniefalco  
> Created_at: 2020-03-19 00:12:36 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394709960  

put the breakpoint in `test_suite::fail`


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-18 22:27:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1881#pullrequestreview-377281291  

📁 test/beast/http/parser.cpp

```diff
 490 |         testIssue818();
 491 |         testIssue1187();
 492 |+         testMultipleContentLengthIssue1880();
```

> Username: vinniefalco  
> Created_at: 2020-03-18 22:27:47 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394675003  

What's wrong with `testIssue1880()` like all the others?

> Username: madmongo1  
> Created_at: 2020-03-18 22:39:50 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394680055  

Does it really matter? I have no idea what issue 818 is about. Whoever comes after me will at least know what is being tested, even if they don't look in github (which may be out of business by then)

> Username: vinniefalco  
> Created_at: 2020-03-19 00:13:28 UTC  
> Updated_at: 2020-03-19 17:20:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#discussion_r394710593  

Yes it matters because it creates noise. If you want to explain what the test does, that's fine - put it in a comment. And I think, you should probably add a link to the relevant HTTP RFC section in the implementation, as a comment.


---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2020-03-19 01:36:55 UTC  
> Updated_at: 2020-03-19 20:23:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1881#issuecomment-600941024  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=h1) Report  
> Merging [#1881](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/7e48270c1bc4a38f5d6bd295eceacfb34a08b83d?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `96.29%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1881/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1881      +/-   ##  
===========================================  
+ Coverage     95.1%   95.11%   +<.01%       
===========================================  
  Files          156      156                
  Lines        12016    12032      +16       
===========================================  
+ Hits         11428    11444      +16       
  Misses         588      588  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/1881/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/1881/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmhwcA==) | `65.38% <100%> (+6.29%)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/1881/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `94.15% <95.65%> (+0.6%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1881/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1881/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.5% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=footer). Last update [7e48270...6b8b54f](https://codecov.io/gh/boostorg/beast/pull/1881?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
