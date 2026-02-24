# #985 Introduce allow_invalid_utf16 option in Boost JSON [Merged]

> Username: vaishnavkatiyar  
> Created at: 2024-02-28 13:58:41 UTC  
> Updated at: 2024-05-06 13:07:37 UTC  
> Merged at: 2024-05-06 13:07:36 UTC  
> Closed at: 2024-05-06 13:07:36 UTC  
> Url: https://github.com/boostorg/json/pull/985  

Fix: [#940 ](https://github.com/boostorg/json/issues/940)  
  
**Motivation**  
Boost library provides parsing and serialization algorithms to transform JSON to and from the [value](https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__value.html) container as needed.  
The current version of the boost JSON library includes an option parse_options::allow_invalid_utf8 to control the treatment of unescaped UTF-8 code units, allowing users to disable the validation of UTF-8 code points.  
It lacks a similar capability for dealing with invalid UTF-16 surrogate pairs. This limitation becomes apparent when users encounter scenarios involving UTF-16 encoded data with mismatched or out-of-range surrogate pairs, where the existing functionality does not provide adequate support or validation mechanisms.  
Therefore, extending this functionality to encompass invalid UTF-16 surrogate pairs is necessary to ensure comprehensive handling of different UTF encoding scenarios and enhance the library's robustness and versatility in parsing and serialization tasks involving UTF-16 encoded JSON data.  
  
**Design**  
The introduction of the parse_options::allow_invalid_utf16 option grants the allowance of invalid UTF-16 surrogate pairs in strings processed by the parser.  
When the option is activated, the parser refrains from raising errors for invalid UTF-16 sequences. Instead, it replaces these invalid sequences with the Unicode replacement character.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-02-28 14:08:07 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1969061551  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-02-28 14:52:30 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1969154574  

This looks like it will affect performance, where's the profile output?

---

## Comment 3

> Username: grisumbras  
> Created_at: 2024-02-28 15:00:22 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1969170937  

It usually appears within an hour. I'm not sure performance will be affected, given that this is a special case within a special case.

---

## Review 4 [Commented]

> Username: grisumbras  
> Created_at: 2024-02-28 15:02:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1906384987  

📁 test/doc_parsing.cpp

```diff
   1 |- //
   1 |+ ﻿//
```

> Username: grisumbras  
> Created_at: 2024-02-28 14:16:44 UTC  
> Updated_at: 2024-02-28 15:02:28 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1506035046  

Please remove BOM.


📁 include/boost/json/basic_parser_impl.hpp

```diff
 566 |-         (opt_.allow_invalid_utf8 << 2))
 566 |+         (opt_.allow_invalid_utf8 << 2) |
 567 |+         (opt_.allow_invalid_utf16 << 3))
```

> Username: grisumbras  
> Created_at: 2024-02-28 14:22:56 UTC  
> Updated_at: 2024-02-28 15:02:28 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1506044742  

I don't think there's any point to this, given that the corresponding parameter is `bool`. Just pass it directly don't need to make the switch larger than it already is.

> Username: vaishnavkatiyar  
> Created_at: 2024-02-29 11:27:44 UTC  
> Updated_at: 2024-02-29 11:27:45 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1507426430  

Passed it directly. Should I merge other cases by passing `opt_.allow_trailing_commas & opt_.allow_invalid_utf8` directly?

> Username: grisumbras  
> Created_at: 2024-02-29 13:28:21 UTC  
> Updated_at: 2024-02-29 13:28:22 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1507575251  

No, don't change old code.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1241 |     char c;
1242 |     cs.clip(temp.max_size());
1243 |+     bool replaced_bad_utf16 = false;
```

> Username: grisumbras  
> Created_at: 2024-02-28 15:02:11 UTC  
> Updated_at: 2024-02-28 15:02:28 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1506109132  

Shouldn't this variable be a data member of the parser, so that suspensions doesn't lose its state?

> Username: vaishnavkatiyar  
> Created_at: 2024-02-29 16:27:47 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1507859622  

Added as a data member to the parser


📁 doc/qbk/io/parsing.qbk

```diff
 108 |     endline characters from the string it produces.]
 109 | 
 110 |+ [caution Enabling the `allow_invalid_utf16` option relaxes strict UTF-16 validation.
```

> Username: grisumbras  
> Created_at: 2024-02-28 14:41:23 UTC  
> Updated_at: 2024-02-28 15:02:28 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1506076551  

I don't think this warning is required. People who enable `allow_invalid_utf16` will understand that the parser will tolerate invalid UTF-16 in their input.

> Username: vaishnavkatiyar  
> Created_at: 2024-02-29 11:25:25 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1507423482  

removed the Warning sign.


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-02-28 15:17:14 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1969206760  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-e5fd9e0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-02-29 16:33:06 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1971515466  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-02-29 17:42:17 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1971642190  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-de51e6e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-03-04 03:13:02 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1975590479  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-03-04 04:22:13 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1975673422  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-4a14c8a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-03-04 06:43:03 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1975838674  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-03-04 07:47:14 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1975914874  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-e10e196.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-03-04 10:49:15 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1976298476  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-03-04 11:57:15 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-1976417606  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-8023f57.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-03-20 08:53:06 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2009057934  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: codecov[bot]  
> Created_at: 2024-03-20 09:14:25 UTC  
> Updated_at: 2024-04-10 12:01:26 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2009091495  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.11%. Comparing base [(`9f85ed6`)](https://app.codecov.io/gh/boostorg/json/commit/9f85ed6d62ff91c6dc4fc30e3a20e9049ec67585?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dc95216`)](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/985/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/985?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #985      +/-   ##  
===========================================  
+ Coverage    93.08%   93.11%   +0.02%       
===========================================  
  Files           87       87                
  Lines         8125     8160      +35       
===========================================  
+ Hits          7563     7598      +35       
  Misses         562      562                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/985?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/985?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.49% <100.00%> (+0.04%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9f85ed6...dc95216](https://app.codecov.io/gh/boostorg/json/pull/985?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2024-03-20 10:02:19 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2009177285  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-965d378.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Review 17 [Changes requested]

> Username: grisumbras  
> Created_at: 2024-03-22 17:14:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1955306430  

In addition to algorithmic changes I requested, please check that you don't create trailing whitespace anywhere.

📁 include/boost/json/basic_parser_impl.hpp

```diff
1374 |+                     unsigned cp = 0xFFFD; // Unicode replacement character
1375 |+                     temp.append_utf8(cp);
1376 |+                     replaced_bad_utf16 = true;
```

> Username: grisumbras  
> Created_at: 2024-03-22 16:53:52 UTC  
> Updated_at: 2024-03-22 17:14:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1535895079  

If this is an implementation of WTF-16 support, then it is my understanding that you need a `break` here. Your implementation transforms two bad surrogates into one U+FFFD. But from my reading of the spec 2 bad surrogates should produce _two_ instances of U+FFFD.  
  
Examples:  
  
\uD800 -> U+FFFD (single high surrogate)  
\uDC00 -> U+FFFD (single low surrogate)  
\uDC00\uD800 -> U+FFFD U+FFFD (low surrogate followed by high surrogate)  
\uDC00\uDC00 -> U+FFFD U+FFFD (low surrogate followed by low surrogate)  
\uD800\uD800 -> U+FFFD U+FFFD (low surrogate followed by high surrogate)  
\uDC00\uD800\uDC00 -> U+FFFD U+10000 (single low surrogate followed by surrogate pair)  
\uD800\uD800\uDC00 -> U+FFFD U+10000 (single high surrogate followed by surrogate pair)

> Username: vaishnavkatiyar  
> Created_at: 2024-03-28 03:35:10 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1542269496  

After making the suggested changes, the result looks like:  
•	\uD800 results in U+FFFD (single high surrogate).  
•	\uDC00 results in U+FFFD (single low surrogate).  
•	\uDC00\uD800 results in U+FFFD U+FFFD (low surrogate followed by high surrogate).  
•	\uDC00\uDC00 results in U+FFFD U+FFFD (low surrogate followed by low surrogate).  
•	\uD800\uD800 results in U+FFFD U+FFFD (high surrogate followed by high surrogate).  
•	\uD800\uDC00 is mapped to U+10000 (high surrogate followed by low surrogate, forming a valid surrogate pair).  
•	\uDC00\uD800\uDC00 results in **U+FFFD U+10000** (evaluated as single low surrogate, high surrogate followed by low surrogate, forming a valid surrogate pair).  
•	\uD800\uDC00\uDC00 results in **U+10000 U+FFFD** (evaluated as high surrogate followed by low surrogate, forming a valid surrogate pair, followed by a single low surrogate).  
  
I have a confusion for the following use case:  
•	\uD800\uD800\uDC00 results in **U+FFFD U+FFFD U+FFFD** (evaluated as high surrogate followed by high surrogate, forming an  invalid surrogate pair followed by single low surrogate).  
It is not evaluated as **U+FFFD U+10000 ** since the first code point is a valid high surrogate and after checking for '**\**' and '**u**' the '**cs**' is pointing to the start of the second code point (**D**). Since we can't move the '**cs**' backwards to '**\**' breaking the switch here will not suffice for checking the second code point (**\uD800**) as a first surrogate for the subsequent code point (**\uDC00**).

> Username: grisumbras  
> Created_at: 2024-03-28 06:02:06 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1542355462  

Yeah, we can't move cs back, but we do have the value of the second code code point. This means we need to add code that checks if the code point was a valid high surrogate. And if it is, fill `temp` and go back to expecting the second surrogate.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1395 |+                 else
1396 |+                 {
1397 |+                     if(replaced_bad_utf16 == false)
```

> Username: grisumbras  
> Created_at: 2024-03-22 17:00:56 UTC  
> Updated_at: 2024-03-22 17:14:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1535907134  

As previously mentioned, if a bad surrogate has been encountered, at this point you should have already been broken out of the switch. Instead, here you should be _replacing_ the contents of `temp` with UTF-8-encoded `0xFFFD`, because, if you are here, than the previous surrogate was good, but now you know it's not a part of a pair.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1442 |+             // Append the second Unicode replacement character if
1443 |+             // the first code point is an illegal leading surrogate
1444 |+             if(replaced_bad_utf16 == true)
```

> Username: grisumbras  
> Created_at: 2024-03-22 17:07:24 UTC  
> Updated_at: 2024-03-22 17:14:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1535915248  

Given that `break` I asked for is added, I don't think this would be necessary.


---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2024-03-28 12:08:13 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2025029676  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2024-03-28 13:17:21 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2025166415  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-7b3299b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-03-28 13:33:12 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2025200998  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2024-03-28 14:42:17 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2025393414  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-e5d5142.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2024-04-02 09:48:10 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2031554837  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2024-04-02 10:57:24 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2031698135  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-7bf5040.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Review 24 [Changes requested]

> Username: grisumbras  
> Created_at: 2024-04-02 17:17:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1968719137  

📁 include/boost/json/basic_parser.hpp

```diff
 315 |     unsigned char cur_lit_ = 0;
 316 |     unsigned char lit_offset_ = 0;
 317 |+     unsigned urc = 0xFFFD; // Unicode replacement character
```

> Username: grisumbras  
> Created_at: 2024-03-29 15:03:50 UTC  
> Updated_at: 2024-04-02 17:17:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1544602378  

Why store this in the class, when it's a constant? Use constexpr local variable.

> Username: vaishnavkatiyar  
> Created_at: 2024-04-03 11:45:19 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549572400  

Okay


📁 include/boost/json/basic_parser_impl.hpp

```diff
1395 |+                 else
1396 |+                 {
1397 |+                     temp.append_utf8(urc);
```

> Username: grisumbras  
> Created_at: 2024-04-02 16:40:11 UTC  
> Updated_at: 2024-04-02 17:17:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1548221977  

I don't think this is correct. At this point we already have the leading surrogate in `temp`. But now we need to remove it (or maybe we shouldn't have put it there in the first place?) and add replacement character instead. Your code doesn't seem to replace.

> Username: vaishnavkatiyar  
> Created_at: 2024-04-03 11:37:02 UTC  
> Updated_at: 2024-04-03 11:37:03 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549558206  

I think we are storing the value of the leading surrogate in **u1** and appending it to **temp** in line **1356** only (when it is a valid utf-8 code point).  
In other cases, we are not appending **u1** to **temp**, hence, directly appending the **replacement character** when:  
1. First code point is NOT a valid high surrogate line **1374**  
2. First code point is a valid high surrogate but second code point is not present line **1397**  
3. First code point is a valid high surrogate but second code point is not a valid low surrogate line **1450**

> Username: grisumbras  
> Created_at: 2024-04-03 12:37:47 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549656861  

Yeah, you're right.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1448 |+                 // first leading surrogate.
1449 |+                 cs += 4;
1450 |+                 temp.append_utf8(urc);
```

> Username: grisumbras  
> Created_at: 2024-04-02 16:45:49 UTC  
> Updated_at: 2024-04-02 17:17:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1548230819  

Again, I'm pretty sure the leading surrogate is already in `temp`.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1460 |+                 {
1461 |+                     u1 = u2;
1462 |+                     goto do_str9;
```

> Username: grisumbras  
> Created_at: 2024-04-02 17:12:08 UTC  
> Updated_at: 2024-04-02 17:17:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1548269359  

If you go there you potentially violate the assumption that `cs.remain() > 10`, and so you can overrun the input buffer.

> Username: vaishnavkatiyar  
> Created_at: 2024-04-03 11:43:10 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549565976  

Since the value of the second code point **u2** is not stored in temp, how can we preserve it if we don't use goto and break instead?

> Username: grisumbras  
> Created_at: 2024-04-03 12:37:28 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549656374  

You can assign to `u1_` and go to `do_sur1`.

---

📁 include/boost/json/basic_parser_impl.hpp

```diff
1659 |         if(BOOST_JSON_UNLIKELY(
1580 |-             u2_ < 0xdc00 || u2_ > 0xdfff))
1660 |+                 u2_ < 0xdc00 || u2_ > 0xdfff))
```

> Username: grisumbras  
> Created_at: 2024-04-02 17:17:18 UTC  
> Updated_at: 2024-04-02 17:17:54 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1548275479  

Why extra indent here?

> Username: vaishnavkatiyar  
> Created_at: 2024-04-03 11:44:16 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1549569642  

Fixed


---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2024-04-04 05:18:07 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2036208795  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2024-04-04 05:38:07 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2036228200  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2024-04-04 06:47:18 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2036320428  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-2cb0b1a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Review 28 [Changes requested]

> Username: grisumbras  
> Created_at: 2024-04-04 17:21:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1980666413  

📁 include/boost/json/basic_parser_impl.hpp

```diff
1396 |+                 {
1397 |+                     temp.append_utf8(urc);
1398 |+                     break;
```

> Username: grisumbras  
> Created_at: 2024-04-04 17:19:28 UTC  
> Updated_at: 2024-04-04 17:21:59 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1552116599  

Add a test that covers this case . Something like `\uD800X` should work.


---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2024-04-06 09:43:07 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2041032950  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2024-04-06 10:52:14 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2041047027  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-cea9e36.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Review 31 [Changes requested]

> Username: grisumbras  
> Created_at: 2024-04-07 17:58:30 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1985128808  

📁 include/boost/json/basic_parser_impl.hpp

```diff
1400 |             }
1401 |             ++cs;
1402 |             if(BOOST_JSON_UNLIKELY(*cs != 'u'))
```

> Username: grisumbras  
> Created_at: 2024-04-07 16:43:41 UTC  
> Updated_at: 2024-04-07 17:58:30 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1555008452  

I think, you are missing a check `allow_invalid_utf16` inside this branch. The condition is valid leading surrogate followed by a non-utf16 escape, e.g. `\uD800\n`.

> Username: grisumbras  
> Created_at: 2024-04-07 17:22:57 UTC  
> Updated_at: 2024-04-07 17:58:30 UTC  
> Url: https://github.com/boostorg/json/pull/985#discussion_r1555014728  

Same for [line 1604](https://github.com/boostorg/json/pull/985/files#diff-ac0f59cf63d51320da1fc37682921e31221528ac2e1c8412363fa3f4d2dead5cR1604)


---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2024-04-08 04:38:06 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2041847850  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2024-04-08 05:47:14 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2041911375  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-ec75acf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Review 34 [Approved]

> Username: grisumbras  
> Created_at: 2024-04-08 09:28:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/json/pull/985#pullrequestreview-1985906008  

Looks alright. Now please rebase on the current develop branch and squash.

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2024-04-08 18:18:07 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2043393990  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2024-04-08 18:38:09 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2043424195  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2024-04-08 18:44:08 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2043434857  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2024-04-08 19:52:12 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2043532619  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-bf9249a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2024-04-10 11:28:03 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2047281650  

An automated preview of the documentation is available at [https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://985.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2024-04-10 12:37:15 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2047430244  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest-condensed-cddafff.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/985/pullrequest.html)

---

## Comment 41

> Username: grisumbras  
> Created_at: 2024-04-11 17:52:17 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2050207294  

Thank you for your work, I'll merge this soon.

---

## Comment 42

> Username: vaishnavkatiyar  
> Created_at: 2024-04-12 10:02:25 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2051453643  

Thank you @grisumbras for your continued support and valuable feedback.

---

## Comment 43

> Username: vaishnavkatiyar  
> Created_at: 2024-04-22 21:45:14 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2071005401  

Hello @grisumbras   
I hope this message finds you well. I wanted to inquire about the anticipated timeframe for the merging of this pull request. Your attention to this matter is greatly appreciated.  
Thank you

---

## Comment 44

> Username: grisumbras  
> Created_at: 2024-04-23 08:56:15 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2071773605  

I need to finish my current project (which will probably happen tomorrow). After that I'll merge this. The change will definitely be in the next Boost release which will happen in August.

---

## Comment 45

> Username: vaishnavkatiyar  
> Created_at: 2024-04-23 09:41:53 UTC  
> Url: https://github.com/boostorg/json/pull/985#issuecomment-2071866712  

Thank you for the update. Looking forward to the next Boost release.

---
