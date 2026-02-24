# #657 NaN and Infinity support [Closed]

> Username: ZFail  
> Created at: 2021-12-03 12:54:22 UTC  
> Updated at: 2023-06-02 08:58:00 UTC  
> Closed at: 2023-06-02 08:58:00 UTC  
> Url: https://github.com/boostorg/json/pull/657  

Аdded support for parsing NaN and Infinity.  
Serializer already supports this.

---

## Comment 1

> Username: grisumbras  
> Created_at: 2021-12-03 13:16:36 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985510872  

As far as I know, neither NaN nor infinity is a part of JSON format

---

## Comment 2

> Username: ZFail  
> Created_at: 2021-12-03 13:55:54 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985540037  

> As far as I know, neither NaN nor infinity is a part of JSON format  
  
Yes, but for example comments too.  
JSON5 is supporting NaN and Infinity.  
Maybe add by option?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-12-03 14:09:09 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985550216  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest-condensed-5344b5d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html)

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-12-03 16:17:41 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985648903  

What do you accept for NaN or infinity?

---

## Comment 5

> Username: ZFail  
> Created_at: 2021-12-03 18:05:56 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985723343  

> What do you accept for NaN or infinity?  
  
JSON  
```  
{  
  "nan": NaN,  
  "infinity": Infinity,  
  "minus_infinity": -Infinity  
}  
```  
parsed as  
```c++  
object obj( {  
  { "nan",  std::numeric_limits<double>::quiet_NaN() },   
  { "infinity", std::numeric_limits<double>::infinity() },   
  { "minus_infinity", -std::numeric_limits<double>::infinity() }  
} );  
```

---

## Comment 6

> Username: grisumbras  
> Created_at: 2021-12-03 18:43:25 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985745958  

This needs to be disabled by default and enabled with an option. See   
https://www.boost.org/doc/libs/1_77_0/libs/json/doc/html/json/ref/boost__json__parse_options.html

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-12-03 18:46:07 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-985747574  

The [JSON5 spec](https://spec.json5.org/#numbers) also allows `+NaN`, `-NaN` and `+Infinity`:  
  
> The IEEE 754 value positive infinity must be the literal characters Infinity and may be prefixed with an optional plus sign.  
> The IEEE 754 value negative infinity must be the literal characters -Infinity.  
> The IEEE 754 value NaN must be the literal characters NaN and may be prefixed with an optional plus or minus sign.

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2023-04-30 14:39:29 UTC  
> Updated_at: 2023-05-03 21:10:55 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529040348  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#657](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9dee834) into [develop](https://app.codecov.io/gh/boostorg/json/commit/ee01580cfde818e31d8ea41c2e4056bcff9e3018?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ee01580) will **decrease** coverage by `0.02%`.  
> The diff coverage is `98.80%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/657/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #657      +/-   ##  
===========================================  
- Coverage    99.19%   99.18%   -0.02%       
===========================================  
  Files           70       70                
  Lines         6851     6996     +145       
===========================================  
+ Hits          6796     6939     +143       
- Misses          55       57       +2       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.36% <98.80%> (+0.02%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ee01580...9dee834](https://app.codecov.io/gh/boostorg/json/pull/657?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-04-30 15:35:58 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529055716  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest-condensed-b4de6b3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html)

---

## Comment 10

> Username: glebov-andrey  
> Created_at: 2023-04-30 16:07:40 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529062596  

Hi!  
I would like to pick up this PR (@ZFail has added me to his repo).  
We need this feature either way, but I'd like to know if there is interest in merging it.  
  
I've already committed optional support for `Infinity`/`NaN` parsing through `parse_options::allow_infinity_and_nan`.  
If there is interest, then I'll add more tests.  
  
Another question is regarding leading `+` and `-NaN` support.  
As @pdimov mentioned, JSON5 supports these.  
But IMO, supporting leading `+` signs specifically for `Infinity` and `NaN` is not a good idea.  
In JSON5 this syntax is supported for numbers in general, which is not true for JSON.  
If there is interest in supporting leading `+` signs, then that should either be extended to all numbers (with the parse option renamed to `allow_extended_numbers`), or implemented separately with it's own parse option.  
  
`-NaN` is more of a grey area.  
It could definitely make sense to support this, given that the sign of a NaN is actually observable using `std::signbit()`.  
But in that case the serializing code should also be modified to produce `-NaN` for such values.  
Otherwise we loose the ability to roundtrip numbers, which is the main point of this feature.

---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2023-05-01 01:32:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/657#pullrequestreview-1407268949  

📁 test/basic_parser.cpp

```diff
 708 |+             make_options(true, false, false, true),
 709 |+             make_options(false, true, false, true),
 710 |+             make_options(true, true, false, true)
```

> Username: vinniefalco  
> Created_at: 2023-05-01 01:32:15 UTC  
> Updated_at: 2023-05-01 01:32:16 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1181340524  

These would look better using 0 and 1 instead of true and false I bet.. lol

> Username: glebov-andrey  
> Created_at: 2023-05-01 11:15:46 UTC  
> Updated_at: 2023-05-01 11:15:47 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1181518222  

Given the growing amount of combinations, I'd rather replace it with a loop (see https://github.com/boostorg/json/pull/657/commits/d665b08517a9d9bbc4475003eb4b5cfa2d920579).

> Username: vinniefalco  
> Created_at: 2023-05-01 13:41:35 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1181579138  

The loop is smaller but it is easier to understand and read when it is written out. And it is easier to set a breakpoint on a particular combination without the loop..

> Username: glebov-andrey  
> Created_at: 2023-05-03 20:52:08 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1184268212  

Ok, done


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2023-05-01 01:32:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/657#pullrequestreview-1407269219  

📁 test/serializer.cpp

```diff
 452 |+             BOOST_TEST(! sr.done());
 453 |+             char buf[32];
 454 |+             BOOST_TEST(sr.read(buf) == "-Infinity");
```

> Username: vinniefalco  
> Created_at: 2023-05-01 01:32:47 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1181340700  

Is this what Javascript emits, `-Infinity` ?

> Username: glebov-andrey  
> Created_at: 2023-05-01 10:59:43 UTC  
> Updated_at: 2023-05-01 10:59:44 UTC  
> Url: https://github.com/boostorg/json/pull/657#discussion_r1181512990  

No, JS emits `null` for infinities and NaNs: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#:~:text=The%20numbers%20Infinity%20and%20NaN%2C%20as%20well%20as%20the%20value%20null%2C%20are%20all%20considered%20null  
  
I'm not actually sure whether these tests should be part of this PR though, considering they are not testing the parser, but decided to leave them just in case for now.


---

## Comment 13

> Username: vinniefalco  
> Created_at: 2023-05-01 01:33:47 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529234457  

This is still not reviewed and merged?

---

## Comment 14

> Username: glebov-andrey  
> Created_at: 2023-05-01 10:53:53 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529570613  

> This is still not reviewed and merged?  
  
Yeah, it was sort of forgotten about for a while.  
Since I needed to rebase this onto 1.82.0 anyway, I decided to take over the PR.  
  
I also left a couple TODOs in the code, if someone could have a look at them:  
https://github.com/boostorg/json/pull/657/commits/2dbb08f3fccbe8599e204655a68e3f256f4959ba#diff-2cc706ac1836b6457314bc5f7f00837fc38310395e9d1486782a82b7d5b6e583R105  
https://github.com/boostorg/json/pull/657/commits/2dbb08f3fccbe8599e204655a68e3f256f4959ba#diff-ac0f59cf63d51320da1fc37682921e31221528ac2e1c8412363fa3f4d2dead5cR769

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-05-01 13:40:40 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529720287  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest-condensed-92a2009.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html)

---

## Comment 16

> Username: glebov-andrey  
> Created_at: 2023-05-01 15:49:56 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1529855871  

The massive switch/case in `basic_parser::parse_document()` is not the most easily extendable thing.  
Here is a template recursion based replacement, if you're interested: https://github.com/ZFail/json/commit/887a3e8223f6935c86e08f58ee35a13ae5117457  
I put it in a separate branch in case such an implementation is not acceptable, e.g. due to debug performance.

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-05-03 22:05:44 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1533812790  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest-condensed-156325d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/657/pullrequest.html)

---

## Comment 18

> Username: grisumbras  
> Created_at: 2023-05-04 13:07:59 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1534752106  

Why specifically `Infinity` and `NaN`? My cursory research shows that the only implementation that outputs these is Python.

---

## Comment 19

> Username: glebov-andrey  
> Created_at: 2023-05-04 19:07:35 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1535269993  

> Why specifically `Infinity` and `NaN`? My cursory research shows that the only implementation that outputs these is Python.  
  
I think the original reasoning is simple - that's what Boost.JSON serializes them as: https://github.com/boostorg/json/blob/ee01580cfde818e31d8ea41c2e4056bcff9e3018/include/boost/json/detail/ryu/detail/common.hpp#L85-L99  
Given that JSON is closely related to JS, and JS has `Infinity` and `NaN` as global properties, this actually seems reasonable.  
That's also probably the reasoning behind JSON5's spelling.

---

## Comment 20

> Username: grisumbras  
> Created_at: 2023-05-05 15:41:30 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1536442253  

While JS has them, it doesn't parse them as numbers. But I guess, this is a common enough approach to pick it.  
  
One question. I'm not sure we actually need the template parameter for whether special number support is enabled. Can you test if the template parameter affects performance and how much it affects binary size?  
  
Also, please rebase on the current develop and squash. after that I will approve CI runs.

---

## Comment 21

> Username: grisumbras  
> Created_at: 2023-05-22 05:51:24 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1556575195  

Are you still planning to do this? I've made a few changes to parsing of JSON literals that makes implementing this change much easier, and also simplifies getting full coverage.

---

## Comment 22

> Username: glebov-andrey  
> Created_at: 2023-05-31 16:52:07 UTC  
> Url: https://github.com/boostorg/json/pull/657#issuecomment-1570582200  

> Are you still planning to do this? I've made a few changes to parsing of JSON literals that makes implementing this change much easier, and also simplifies getting full coverage.  
  
I'm really sorry! The last few weeks have been rather busy for me, and I missed this comment.  
I take it, this PR has now been superseded by #899, and so there's no point in doing anything here?  
  
Regarding replacing the template parameter with a runtime one:  
I had initially assumed that the non-conditional checks were the reason for the performance regressions seen in https://github.com/boostorg/json/pull/657#issuecomment-985550216, but clearly that's not the case since #899 is doing fine.  
A quick binary size comparison with Windows/Clang/Release-LTO shows a 134KiB improvement for the test binary, so runtime checks seem like the way to go.  
  
I'd be glad to help with anything else I can regarding this

---
