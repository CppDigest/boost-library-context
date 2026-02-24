# #2886 `const`-qualify trivial getters [Closed]

> Username: sameer-here  
> Created at: 2024-06-12 11:24:54 UTC  
> Updated at: 2024-06-14 16:53:19 UTC  
> Closed at: 2024-06-14 16:53:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2886  

None of these methods modify `serializer` members, so they ought to be `const`.  This undoes https://github.com/boostorg/beast/commit/8a3df73ffc7b2a1bf89f624fdd9a26c9dcf1fb5c

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-12 12:25:52 UTC  
> Updated_at: 2024-06-12 12:46:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2162880192  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2886?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.23%. Comparing base [(`e7f4919`)](https://app.codecov.io/gh/boostorg/beast/commit/e7f49190ef12cbda6c809624b8c10a05f8c8a284?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2664e37`)](https://app.codecov.io/gh/boostorg/beast/commit/2664e3727aa1c6c2303f2b475d9cb8c3051c4d41?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2886/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2886?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2886   +/-   ##  
========================================  
  Coverage    93.23%   93.23%             
========================================  
  Files          177      177             
  Lines        13675    13675             
========================================  
  Hits         12750    12750             
  Misses         925      925             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2886?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2886?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `90.90% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2886?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2886?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e7f4919...2664e37](https://app.codecov.io/gh/boostorg/beast/pull/2886?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ashtum  
> Created_at: 2024-06-12 12:57:29 UTC  
> Updated_at: 2024-06-12 13:31:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2162946023  

I believe the changes in https://github.com/boostorg/beast/commit/8a3df73ffc7b2a1bf89f624fdd9a26c9dcf1fb5c were intentional because these functions might return different values depending on the state of the serializer object during write operations, making them logically non-const. However, `is_done()` was made const again in https://github.com/boostorg/beast/pull/2428/commits/344c10ff1ea1a31ded7a4481e0d1479d4c9d5886, so this might not be the reason.  
  
@vinniefalco, could you please shed some light on this?

---

## Comment 3

> Username: sameer-here  
> Created_at: 2024-06-13 11:20:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2165365828  

> I believe the changes in https://github.com/boostorg/beast/commit/8a3df73ffc7b2a1bf89f624fdd9a26c9dcf1fb5c were intentional because these functions might return different values depending on the state of the serializer object during write operations, making them logically non-const.  
  
If these methods are "logically non-const" I'd prefer it be explained by documentation rather dropping the `const`, since the methods don't modify the members of the class.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2024-06-13 15:25:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2166000369  

> None of these methods modify serializer members, so they ought to be const  
  
That may be true, but you as a user are not supposed to know or care about that. All you need to know is that the functions may not be called concurrently, this is by design. Labeling them const would imply that they are thread-safe. While it is true that the current implementation is thread-safe, that is an implementation detail. The library reserves the right to change the implementation.  
  
As a user, you should not care whether these members are const or not. If there is a specific problem that your patch solves, then please show us the example code or describe the scenario.

---

## Comment 5

> Username: sameer-here  
> Created_at: 2024-06-13 17:09:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2166336622  

> Labeling them const would imply that they are thread-safe.  
  
My impression is that `const` is orthogonal to thread-safe.  I would not expect thread safety from a method just because it is marked `const`.  I would expect that it is safe to call the `const` method from multiple threads, but to call a non-const method and a `const` method from different threads would cause a data race, unless the documentation specified that the class was thread safe.  If my understanding of `const` is wrong, I would be happy to learn!  
  
> As a user, you should not care whether these members are const or not. If there is a specific problem that your patch solves, then please show us the example code or describe the scenario.  
  
I tend to agree that there's no massive issue caused by these being `non-const`.  I did have some issues when `is_done` was not marked `const`, but `is_header_done()`, `split()`, and `limit()` aren't problematic.  This is just a minor quality-of-life patch.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2024-06-13 17:56:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2166448849  

https://stackoverflow.com/questions/14127379/does-const-mean-thread-safe-in-c11

---

## Comment 7

> Username: sameer-here  
> Created_at: 2024-06-14 15:22:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2168263922  

> [17.6.5.9/3] A C++ standard library function shall not directly or indirectly modify objects (1.10) accessible by threads other than the current thread unless the objects are accessed directly or indirectly via the function’s non-const arguments, including this  
  
I think that stackoverflow answer above puts its pretty concisely, that  
  
> This means that the Standard Library won't introduce a data race as long as operations on const objects of your own types either  
> 1. Consist entirely of reads --that is, there are no writes--; or  
> 2. Internally synchronizes writes.  
  
`const`-qualifying a getter would fall under the first point.  The `const` doesn't imply the non-`const` methods of the class are thread-safe, merely that concurrently calling the `const` operations is safe.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2024-06-14 16:44:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2886#issuecomment-2168407637  

> The `const` doesn't imply the non-`const` methods of the class are thread-safe, merely that concurrently calling the `const` operations is safe.  
  
Yes, exactly. This is precisely why I do not want them to be marked `const`: I do not want to promise to the user that calling those members concurrently is safe. It is true that the current implementation makes it safe today, but the library should have the freedom to change this in the future.  
  
The principle here is that the API should make as few promises as possible while achieving the intended purpose. Instances of a parser or serializer are never intended to be used concurrently, and there is no need for such. Thus we avoid offering the user the guarantee of safe concurrency even for trivial functions whose current implementations cannot possibly race.

---
