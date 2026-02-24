# #118 Resolve visual studio warning C4127: conditional expression is constant. [Merged]

> Username: dgeelen  
> Created at: 2020-06-25 08:20:32 UTC  
> Updated at: 2021-02-16 15:30:01 UTC  
> Merged at: 2021-02-16 15:30:01 UTC  
> Closed at: 2021-02-16 15:30:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118  

This resolves a warning in Visual Studio which is currently prevents using `boost::iostreams::stream<>` when this warning is enabled.  
  
What seems to happen is that the first part of the expression is recognized as constant, but somehow the second part is not taken into account to see that the expression as a whole is not constant. Flipping the two around fixes the warning, but not the problem. In principle this code should still trigger the warning, because you probably want to know when a sub-expression in an if() statement is constant too. Because a constant sub-expression seems just as wrong a a constant full expression.  
  
However, this seems to solve the warning without any changes to the logic, so this is a simple fix for now.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-25 16:55:05 UTC  
> Updated_at: 2020-07-28 17:04:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-649699243  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=h1) Report  
> Merging [#118](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/bc868902b0e42a0ff7440d52c584a14a04d0fad3&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/118/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #118   +/-   ##  
========================================  
  Coverage    68.84%   68.84%             
========================================  
  Files           80       80             
  Lines         3444     3444             
  Branches      1027     1027             
========================================  
  Hits          2371     2371             
  Misses         454      454             
  Partials       619      619             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [.../iostreams/detail/streambuf/indirect\_streambuf.hpp](https://codecov.io/gh/boostorg/iostreams/pull/118/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL3N0cmVhbWJ1Zi9pbmRpcmVjdF9zdHJlYW1idWYuaHBw) | `75.30% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=footer). Last update [bc86890...726cbe1](https://codecov.io/gh/boostorg/iostreams/pull/118?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: dgeelen-uipath  
> Created_at: 2020-07-06 07:39:35 UTC  
> Updated_at: 2020-07-06 07:40:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-654070023  

So, what's happening here? Is there an actual problem or can this be merged? It seems to me that this a false positive in the codecov report? @mclow

---

## Comment 3

> Username: dgeelen-uipath  
> Created_at: 2020-07-20 07:41:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-660860697  

ping  
@Lastique @mclow @jeking3 @pdimov

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-07-20 08:42:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-660890634  

1. I'm not the maintainer, and I cannot merge this.  
2. I wouldn't merge because I would prefer to disable the warning with a pragma rather than modifying the condition. We actually want the conditional expression to be constant.

---

## Comment 5

> Username: dgeelen-uipath  
> Created_at: 2020-07-21 12:44:52 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-661835949  

>     2. I wouldn't merge because I would prefer to disable the warning with a pragma rather than modifying the condition. We actually want the conditional expression to be constant.  
  
But I didn't change that, the condition is still constant (and exactly the same condition as before). All I did was to swap the left and right-hand side of the equality. This is sufficient to suppress the warning in Visual Studio, as it seems to only scan for 'unconditional constantness'. I.e. a constant after a run-time check is not considered constant.  
  
Given that I didn't make any changes to the actual logic, what are your thoughts on the CodeCov report?

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-07-21 13:21:50 UTC  
> Updated_at: 2020-07-21 13:25:29 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-661857938  

> All I did was to swap the left and right-hand side of the equality.  
  
You changed the order of operands of `operator||`, which makes it non-constant. Which is probably why the compiler stopped complaining. See [logical operators short-circuiting](https://en.wikipedia.org/wiki/Short-circuit_evaluation).

---

## Comment 7

> Username: dgeelen-uipath  
> Created_at: 2020-07-23 10:35:52 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-662935017  

> > All I did was to swap the left and right-hand side of the equality.  
>   
> You changed the order of operands of `operator||`, which makes it non-constant. Which is probably why the compiler stopped complaining. See [logical operators short-circuiting](https://en.wikipedia.org/wiki/Short-circuit_evaluation).  
  
I didn't really see that as a problem, but if you're worried about e.g. performance than I'm also fine with disabling it through a `#pragma`. It's just that I preferred to resolve the problem without relying on compiler specifics.  
  
Please see my updated commit for the `#pragma` version.

---

## Comment 8

> Username: Lastique  
> Created_at: 2020-07-23 10:43:21 UTC  
> Updated_at: 2020-07-23 10:45:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-662937760  

The pragmas must be guarded with `defined(BOOST_MSVC)`, for which `boost/config.hpp` must be included. Or better yet, disable/restore the warning globally in `boost/iostreams/detail/config/disable_warnings.hpp` and `boost/iostreams/detail/config/enable_warnings.hpp`.  
  
Actually, I can see that this warning is already [disabled](https://github.com/boostorg/iostreams/blob/bc868902b0e42a0ff7440d52c584a14a04d0fad3/include/boost/iostreams/detail/config/disable_warnings.hpp#L13) there. So I'm not sure why you're having this problem. What is your compiler? Does the problem reproduce with the current develop branch?

---

## Comment 9

> Username: dgeelen-uipath  
> Created_at: 2020-07-28 14:58:49 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-665091474  

I've added the guards around the offending code.  
  
I don't quite get how MSVC parses this, but perhaps it has something to do with the fact that this code is inside a template? If I don't put the `#pragma` inside of the template (near the offending code), it doesn't seem to want to disable. Even if I put it _after_ the `#include` at the end that pops the warning state it will still trigger. Although maybe some other header somewhere is turning it on again, as I *can* disable by putting the `#pragma` just after the `#include <boost/iostreams/stream.hpp>` in my code.  
  
This is with Visual Studio Enterprise 2019 Preview - Version 16.7.0 Preview 5.0 / `Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29109 for x64`. I don't think there is any real difference between 1.73 and develop for this header.

---

## Comment 10

> Username: Lastique  
> Created_at: 2020-07-28 15:04:29 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-665094859  

I'd recommend [reporting](https://developercommunity.visualstudio.com/spaces/8/index.html) this to Microsoft as it doesn't look like a correct behavior of the pragma to me.

---

## Comment 11

> Username: pdimov  
> Created_at: 2020-07-28 15:28:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-665108514  

I see nothing wrong with disabling the warning locally like this.

---

## Comment 12

> Username: Lastique  
> Created_at: 2020-07-28 16:00:14 UTC  
> Url: https://github.com/boostorg/iostreams/pull/118#issuecomment-665126361  

It shouldn't be needed as it is already disabled.  
  
Though as a workaround for a compiler bug it's probably fine. I would also add a comment explaining why this workaround is needed.

---
