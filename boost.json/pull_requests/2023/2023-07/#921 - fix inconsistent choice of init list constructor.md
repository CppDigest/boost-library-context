# #921 fix inconsistent choice of init list constructor [Merged]

> Username: grisumbras  
> Created at: 2023-07-21 15:18:52 UTC  
> Updated at: 2023-08-25 20:30:53 UTC  
> Merged at: 2023-08-25 17:54:49 UTC  
> Closed at: 2023-08-25 17:54:49 UTC  
> Url: https://github.com/boostorg/json/pull/921  

On some compilers `value{x}` invokes `initializer_list` constructor, on others it is equivalent to `value(x)`. This is very problematic, but this isn't something we can fix. On the other hand, we _can_ make init list construction to be equivalent to `value(x)`, if the size of init list is one. This commit does exactly that.  
  
Fix #920

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-21 16:35:44 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1645962556  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-2b847f4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-07-21 17:19:42 UTC  
> Updated_at: 2023-08-25 17:47:40 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646018093  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#921](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (25e740d) into [develop](https://app.codecov.io/gh/boostorg/json/commit/2acdb29a328fcc65babe1fd9ee2767195d8309ab?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2acdb29) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/921/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #921   +/-   ##  
========================================  
  Coverage    92.95%   92.96%             
========================================  
  Files           85       85             
  Lines         8052     8056    +4       
========================================  
+ Hits          7485     7489    +4       
  Misses         567      567             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.92% <ø> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2acdb29...25e740d](https://app.codecov.io/gh/boostorg/json/pull/921?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-07-21 17:41:56 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646041511  

Hey good idea !

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-21 18:10:41 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646071459  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-6520fb0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---

## Comment 5

> Username: nkolotov  
> Created_at: 2023-07-22 13:36:14 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646585860  

¿What if caller actually wants to produce an array with a single item? Some people may already be using code that supplies `initializer_list` explicitly expecting array to be produced.  
```  
void Pack(::std::initializer_list<::boost::json::value_ref> items)  
{  
    ::boost::json::value json{items};  
    ::boost::json::array & array{json.as_array()};  
    // do something with array...  
}  
```  
And proposed change will break this code when only a single item is supplied.  
  
Also i'm not sure about `::new(this) value (...`. ¿Is an attempt to reuse storage of the object which lifetime hasn't started yet a well-defined behavior? ¿Will access to newly created object through reference to the old one be valid?

---

## Comment 6

> Username: grisumbras  
> Created_at: 2023-07-23 18:24:41 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646923465  

The proposed change is the only way I could come up with that would make the behaviour consistent. The problem of behaviour being inconsistent between compilers have been haunting us for years.  
  
That being said, creating an array from one element init list is very easy:  
```  
std::initializer_list<value_ref> items = {x};  
value jv = array(items);  
```   
or  
```  
value jv = array{x};  
```

---

## Comment 7

> Username: grisumbras  
> Created_at: 2023-07-23 18:26:32 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1646923903  

> Also i'm not sure about `::new(this) value (...`. ¿Is an attempt to reuse storage of the object which lifetime hasn't started yet a well-defined behavior? ¿Will access to newly created object through reference to the old one be valid?  
  
How could there be a reference to an object that does not exist yet?

---

## Comment 8

> Username: nkolotov  
> Created_at: 2023-07-26 21:43:00 UTC  
> Updated_at: 2023-07-26 21:43:33 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1652537065  

> The proposed change is the only way I could come up with that would make the behaviour consistent.  
  
However you make behavior of constructor taking `initializer_list` inconsistent. Potentially breaking existing code.  
  
> How could there be a reference to an object that does not exist yet?  
  
In case if `::new(this)`, `this` is a pointer to the object being currently constructed. And if object here turns out to be not transparently replaceable then `this` pointer as well as any other names referring to the current object will become invalid. Also even if object *is* transparently replaceable, `::new(this)` is dangerous in other ways since `::boost::json::value` is not trivially destructible type.

---

## Comment 9

> Username: grisumbras  
> Created_at: 2023-07-31 13:22:03 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1658363830  

> However you make behavior of constructor taking `initializer_list` inconsistent. Potentially breaking existing code.  
  
No, it will not make it inconsistent. It will be consistent with documented behaviour (which reminds me that I should have amended documentation). And yes, it will break existing code. So, I'm leaning towards temporary providing a macro to disable the new behaviour.   
  
> In case if `::new(this)`, `this` is a pointer to the object being currently constructed. And if object here turns out to be not transparently replaceable then `this` pointer as well as any other names referring to the current object will become invalid. Also even if object _is_ transparently replaceable, `::new(this)` is dangerous in other ways since `::boost::json::value` is not trivially destructible type.  
  
Ok, just to be sure I will replace `new(this)` with a different implementation.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-08-01 17:40:54 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1660801290  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-9803293.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-08-23 18:58:22 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1690483983  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-b3f4c74.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-08-23 20:55:42 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1690626764  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-d65c424.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-08-25 20:30:53 UTC  
> Url: https://github.com/boostorg/json/pull/921#issuecomment-1693896357  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest-condensed-032dca0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/921/pullrequest.html)

---
