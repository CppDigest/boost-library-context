# #200 Feature/detect reentrancy [Merged]

> Username: joaquintides  
> Created at: 2023-07-24 18:32:15 UTC  
> Updated at: 2024-08-17 16:48:09 UTC  
> Merged at: 2023-08-12 09:58:29 UTC  
> Closed at: 2023-08-12 09:58:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/200  



---

## Comment 1

> Username: joaquintides  
> Created_at: 2023-07-24 18:35:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648405348  

PR open for discussion:  
  
* Tests are already added.  
* There's a rather extensive protocol in place for enabling/disabling/configuring this checking.  
  * Maybe overkill?  
  * Should we document it? (I'd rather not, much as we haven't for `BOOST_UNORDERED_(DISABLE|ENABLE)_SSE2`).  
* I took a look at `__attribute__((capability("mutex")))` and am not sure how the thing is supposed to be used and whether we have to conditionally add it or not.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-07-24 18:38:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648409758  

I don't think you should duplicate all the BOOST_ASSERT functionality. That's what it's for.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-07-24 18:40:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648413819  

For the attribute, see https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#mutex-h, the `Mutex` class.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2023-07-24 18:41:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648416623  

> I don't think you should duplicate all the BOOST_ASSERT functionality. That's what it's for.  
  
Do we at least keep `BOOST_UNORDERED_DISABLE_REENTRANCY_CHECK`, or not even that?

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-07-24 18:42:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648418871  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-07-24 18:43:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1648422801  

Yes, we should have a way to disable the reentrancy check independently, for performance reasons.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-07-25 07:27:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1649272361  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2023-07-26 06:09:53 UTC  
> Updated_at: 2023-07-28 21:51:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1651037859  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#200](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1979ce9) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/bd24dfd284dbc70e7521915af0d8d049f74a1e85?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bd24dfd) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/200/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #200   +/-   ##  
========================================  
  Coverage    97.87%   97.87%             
========================================  
  Files          128      130    +2       
  Lines        18642    18694   +52       
========================================  
+ Hits         18245    18297   +52       
  Misses         397      397             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.53% <100.00%> (ø)` | |  
| [...de/boost/unordered/detail/foa/reentrancy\_check.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9yZWVudHJhbmN5X2NoZWNrLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/reentrancy\_check\_test.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3JlZW50cmFuY3lfY2hlY2tfdGVzdC5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bd24dfd...1979ce9](https://app.codecov.io/gh/boostorg/unordered/pull/200?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-07-26 10:57:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1651550299  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-07-26 15:02:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1651991302  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 11

> Username: cmazakas  
> Created_at: 2023-07-26 22:26:39 UTC  
> Updated_at: 2023-07-26 22:27:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1652621631  

This looks good to me as well.  
  
I feel like we should document the `BOOST_UNORDERED_DISABLE_REENTRANCY_CHECK` macro, no? It seems like it's automatically disabled in release builds when `NDEBUG` is defined but I think it'd be good if users know about this configuration macro in particular.  
  
If we also wish to keep the same style of test, there's the `UNORDERED_AUTO_TEST` macro which can be used but I don't really think this is a big deal at the end of the day.

---

## Comment 12

> Username: joaquintides  
> Created_at: 2023-07-28 11:41:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655540614  

Regarding support of Clang thread safety analysis:  
  
I've written a draft of what it'd take to support it [here](https://github.com/boostorg/unordered/commit/f85ec3e9fb02b82cff1bcd5b4ce7789efbef0866). I've found severe limitations:  
  
**Reentrancy detection**   
The anaysis is strictly [intra-procedural](https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#no-inlining). So, reentrancy is not detected here:  
  
```cpp  
using mutex=boost::unordered::detail::foa::annotated_mutex<boost::unordered::detail::foa::rw_spinlock>;  
using lock_guard=boost::unordered::detail::foa::reentrancy_checked<boost::unordered::detail::foa::lock_guard<mutex>>;  
  
static mutex m;  
  
void foo()  
{  
  lock_guard lck{nullptr,m};  
}  
  
void bar()  
{  
  lock_guard lck{nullptr,m};  
  foo();  
}  
  
int main()  
{  
  bar();  
}  
```  
and the following explicit annotation is needed:  
```cpp  
void foo() BOOST_UNORDERED_EXCLUDES(m)  
{  
  lock_guard lck{nullptr,m};  
}  
  
void bar()  
{  
  lock_guard lck{nullptr,m};  
  foo(); // warning : cannot call function 'foo' while mutex 'm' is held [-Wthread-safety-analysis]  
}  
```  
which is a PITA as we'd need to annotate all the internal and external functions of `foa::concurrent_table`. Even so, the following reentrancy is again not detected:  
```cpp  
void foo() BOOST_UNORDERED_EXCLUDES(m)  
{  
  lock_guard lck{nullptr,m};  
}  
  
template<typename F>  
void bar(F f)  
{  
  lock_guard lck{nullptr,m};  
  f();  
}  
  
int main()  
{  
  bar([]{foo();});  
}  
```  
because the lambda function is not annotated --and can't be, being user code. So, this Clang's analysis is useless for detecting reentrancies in user code.  
  
**Race detection**  
  
To detect unprotected access to data, the analysys tool requires annotations like this:  
```cpp  
static mutex m;  
static int balance BOOST_UNORDERED_GUARDED_BY(m);  
```  
We can't have this annotation in our data structure:  
* Container level: [protected data](https://github.com/boostorg/unordered/blob/bd24dfd284dbc70e7521915af0d8d049f74a1e85/include/boost/unordered/detail/foa/core.hpp#L1786-L1787) lives inside base class `foa::table_core`, which can't access the mutex in the derived class (except with incredible contortions).  
* Group level: there is no way to tell the analysis tool that the _n_-th group in the `group` array is guarded by the _n_-th mutex in the `group_access` array: annotations can only use compile time expressions (typically, simple references to data members).  
  
For all these reasons, I think we should abandon this line.

---

## Comment 13

> Username: pdimov  
> Created_at: 2023-07-28 12:24:19 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655598460  

> For all these reasons, I think we should abandon this line.  
  
No objections from me.

---

## Comment 14

> Username: joaquintides  
> Created_at: 2023-07-28 15:05:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655851192  

> I feel like we should document the BOOST_UNORDERED_DISABLE_REENTRANCY_CHECK macro, no? It seems like it's automatically disabled in release builds when NDEBUG is defined but I think it'd be good if users know about this configuration macro in particular.  
  
I'd rather leave this undocumented (and so little publicized), just as we did with `BOOST_UNORDERED_DISABLE_SSE2` etc. I don't think there'll be many situations where the user may legitimately need to disable it (`entry_trace` lists will typically be <=2 in size), and there are situations where it can be unlegitimately disabled to let code pass that we have officially banned.

---

## Comment 15

> Username: pdimov  
> Created_at: 2023-07-28 15:11:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655864561  

I agree that `BOOST_UNORDERED_DISABLE_REENTRANCY_CHECK` should be documented. It's not an implementation detail or a testing artifact; its purpose is to be user-settable, and it has a specific and legitimate use - to disable reentrancy checking in debug builds for performance reasons.

---

## Comment 16

> Username: joaquintides  
> Created_at: 2023-07-28 15:19:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655876508  

Well, I begrudginly concede :-) There's two places where this can be documented:  
  
* In the [tutorial](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html#concurrent_visitation_based_api), which is less official.  
* [Here](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html#concurrent_flat_map_concurrency_requirements_and_guarantees), as an implementation note (more official).  
  
Opinions?

---

## Comment 17

> Username: pdimov  
> Created_at: 2023-07-28 15:23:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655880888  

My suggestion is to either add a "Configuration Macros" subsection to the reference (after the "Guarantees") and put it there, or add a dedicated "Reentrancy Detection" section.

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-07-28 16:32:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1655975324  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-08-12 08:02:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/200#issuecomment-1675776796  

An automated preview of the documentation is available at [https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://200.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
