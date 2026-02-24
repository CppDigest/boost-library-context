# #24 Missing visibility mark on exception type [Merged]

> Username: Kojoley  
> Created at: 2018-10-17 18:18:03 UTC  
> Updated at: 2018-10-25 12:40:19 UTC  
> Merged at: 2018-10-25 12:32:30 UTC  
> Closed at: 2018-10-25 12:32:30 UTC  
> Url: https://github.com/boostorg/function/pull/24  



---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-17 18:21:25 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430735357  

Seems to me we're missing a test case here that needs to be put in, demonstrating the issue (without the fix, or if someone changes the code in the future) and proving this fix?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-10-17 18:24:18 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430736325  

I thought about test, but it requires more then pencil edit on GH :)

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-10-17 18:53:46 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430746832  

I do not think that we really need a test here. The `BOOST_SYMBOL_VISIBLE` macro was created exactly for [that purpose](https://www.boost.org/doc/libs/1_68_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_for_libraries_with_separate_source_code).

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-10-17 19:03:25 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430750186  

AMDG  
  
On 10/17/2018 12:53 PM, Antony Polukhin wrote:  
> I do not think that we really need a test here. The `BOOST_SYMBOL_VISIBLE` macro was created exactly for [that purpose](https://www.boost.org/doc/libs/1_68_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_for_libraries_with_separate_source_code).  
>   
  
I don't think that's what James meant by test.  
The important part is to demonstrate why this  
particular symbol needs to be visible.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-10-17 19:05:51 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430750936  

> I don't think that's what James meant by test.  
> The important part is to demonstrate why this  
> particular symbol needs to be visible.  
  
https://gcc.gnu.org/wiki/Visibility#Problems_with_C.2B-.2B-_exceptions_.28please_read.21.29

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-17 19:15:06 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430753915  

UBSan is capable of catching this type of problems but because of a false positive https://bugs.llvm.org/show_bug.cgi?id=39191 it is unusable with `-fvisibility=hidden` flag.

---

## Comment 7

> Username: swatanabe  
> Created_at: 2018-10-17 19:42:18 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430762030  

AMDG  
  
On 10/17/2018 01:15 PM, Nikita Kniazev wrote:  
> UBSan is capable of catching this type of problems but because of a false positive https://bugs.llvm.org/show_bug.cgi?id=39191 it is unusable with `-fvisibility=hidden` flag.  
>   
  
Whether that counts as a false positive is questionable.  
The problem is that there is no clear formal definition  
of what the one definition rule means in the presence  
of -fvisibility=hidden.  According to the simplest  
definition by which hidden symbols in different modules  
are distinct with respect to the ODR, the code in that  
issue clearly has undefined behavior and UBSan is correct  
to flag it.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2018-10-17 23:16:49 UTC  
> Updated_at: 2018-10-17 23:18:17 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430821835  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=h1) Report  
> Merging [#24](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/453860ff9cfac59c53bd3936e95324b2e6318b97?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/24/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #24   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/function/function\_base.hpp](https://codecov.io/gh/boostorg/function/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl9iYXNlLmhwcA==) | `44.71% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=footer). Last update [453860f...674cc11](https://codecov.io/gh/boostorg/function/pull/24?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-10-17 23:37:20 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430825650  

Given `bad_function_call` is likely invoked and does indeed need to be a visible symbol, the change looks okay to me.  It would be nice to have been able to find something like this through CI testing.  It is quite possible that `bad_function_call` is never actually tested (with shared libraries?) in the CI environment.

---

## Comment 10

> Username: Kojoley  
> Created_at: 2018-10-17 23:39:21 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-430826016  

https://github.com/boostorg/function/blob/51145b9d2c875414abe6d65d4b437a73174582f0/test/function_test.cpp#L669-L679

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-10-18 13:54:11 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431017562  

So this would only be necessary if a shared library uses boost function in its visible interface (or had a bug where it could throw that exception out the interface, even if the consumer had no idea boost::function was being used internally - but in this case one would need to at least document the possibility), correct?  Even if the consumer caught std::runtime_exception which is declared visible, the visibility of bad_function_call still needs to be default?  (I'm just trying to familiarize myself with the visibility rules, since uuid has a new entropy_error exception that may need the same treatment).

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-10-18 15:13:40 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431048266  

I do not have good understanding even after reading any available info. The main problem is that it is all about implementation defined behavior. IIUC there will be no problem with catching `std::runtime_exception` because `boost::bad_function_call` is not virtually inherited (but [it should?](https://stackoverflow.com/a/14364055/3621421)) from it, that's why compiler will skip `dynamic_cast` and will catch `std::runtime_exception` without problems as its symbol is available in stdlib DSO. However, I am afraid of what will happen if a user will inherit (virtually?) its exception from `boost::bad_function_call` and will try to catch the base.

---

## Comment 13

> Username: apolukhin  
> Created_at: 2018-10-18 16:49:05 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431082120  

You could reproduce the problem in a simpler way. You will need a shared library build with hidden visibility. From within that library throw a `boost::bad_function_call` exception and try to catch `boost::bad_function_call` in `main`.  
  
If the `boost::bad_function_call` not marked with `BOOST_SYMBOL_VISIBLE`, then you won't be able to catch it (or you will have a big performance degradation). That's because with hidden visibility you have two different vtables for `boost::bad_function_call` - one in the shared library and another one in your executable.  
  
With `BOOST_SYMBOL_VISIBLE` the dynamic linker will note two equal vtables and will merge them into one entity.

---

## Comment 14

> Username: Kojoley  
> Created_at: 2018-10-18 16:53:26 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431083588  

@apolukhin I was writing the same, but but before posting tried myself and it worked without problems on both GCC and Clang (I get fails only when make inheritance virtual).

---

## Comment 15

> Username: apolukhin  
> Created_at: 2018-10-18 17:00:21 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431085909  

Yes, but your performance is probably degraded. Without `BOOST_SYMBOL_VISIBLE` there will be two different type_infos, so the implementation falls back to comparing types by strcmping their mangled names rather than jsut comparing two type_info pointers.

---

## Comment 16

> Username: Kojoley  
> Created_at: 2018-10-22 15:54:19 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-431876766  

> Yes, but your performance is probably degraded.   
  
I performed a benchmark and it did not show any difference. Can you look at it https://github.com/Kojoley/catch-hidden-exception-benchmark, maybe I miss something?  
```  
GCC 6.3  
----------------------------------------------------  
Benchmark             Time           CPU Iterations  
----------------------------------------------------  
hidden             2301 ns       2299 ns     305246  
visible            2263 ns       2263 ns     309187  
hidden_base        2286 ns       2284 ns     301546  
visible_base       2293 ns       2293 ns     302324  
  
Clang 3.8  
----------------------------------------------------  
Benchmark             Time           CPU Iterations  
----------------------------------------------------  
hidden             2437 ns       2437 ns     284839  
visible            2506 ns       2505 ns     276001  
hidden_base        2449 ns       2448 ns     288550  
visible_base       2431 ns       2430 ns     284469  
  
Clang 7  
----------------------------------------------------  
Benchmark             Time           CPU Iterations  
----------------------------------------------------  
hidden             2422 ns       2422 ns     289890  
visible            2393 ns       2393 ns     295283  
hidden_base        2425 ns       2425 ns     296866  
visible_base       2390 ns       2390 ns     294539  
```

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-10-25 12:06:49 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-433024872  

The current behavior therefore can be explained by inlining (where both the library and the consumer have an identical implementation of the exception)?  So perhaps an interesting experiment here would be to define the exceptions's constructor in a separate implementation file (thereby eliminating automatic inlining) and without `BOOST_SYMBOL_VISIBLE` I would expect that to fail.  Inlining is not guaranteed.  
  
At this point however do we need to do anything else to prove or disprove the behavior?  It would seem like a good idea to put `BOOST_SYMBOL_VISIBLE` on an exception that can be passed out of a shared library, since there's no guarantee the consumer will inline the exception's body.

---

## Comment 18

> Username: Kojoley  
> Created_at: 2018-10-25 12:24:54 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-433030012  

> The current behavior therefore can be explained by inlining (where both the library and the consumer have an identical implementation of the exception)?  
  
I cannot find references in GCC documentation, but Niall Douglas said:  
  
> That resulted in a problem where if base class visibility did not   
> match derived class visibility then you got two copies of the base   
> class typeinfo generated, and the catch(...) implementation   
> historically used to only compare typeinfo pointers, not their   
> contents. **That got fixed by changing GCC to always compare typeinfo**   
> **strings**, so throwing and catching exceptions became much slower. LLVM   
> chose to keep exception types not being matched. What really *should*   
> have happened is that std::exception and all the STL exceptions must   
> always have default visibility, and indeed it normally does in most   
> implementations. I personally would have preferred if the compiler   
> permanently marked default visibility on any type, or relation   
> thereof, ever thrown or caught. But the ship sailed on that too.   
  
http://boost.2283326.n4.nabble.com/BOOST-SYMBOL-VISIBLE-td4673673.html  
  
That also explains my benchmark results.  
  
And I find out today that Clang with libc++ cannot catch exception without visibility mark. Also Mac users already had the problem with catching boost exceptions for some time https://svn.boost.org/trac10/ticket/10899 https://svn.boost.org/trac10/ticket/11070.

---

## Comment 19

> Username: jeking3  
> Created_at: 2018-10-25 12:32:14 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-433032254  

Okay, sorry to drag everyone through all that work on a single line change that made sense.  This looks like an issue the standards committee needs to address.  It looks like it's a bit of a mess.

---

## Comment 20

> Username: Kojoley  
> Created_at: 2018-10-25 12:40:19 UTC  
> Url: https://github.com/boostorg/function/pull/24#issuecomment-433034641  

> And I find out today that Clang with libc++ cannot catch exception without visibility mark.  
  
To confirm my words https://github.com/Kojoley/function/commit/9f93c574b8aaeae542900f1d2c380b4ce4ec5edc https://travis-ci.org/Kojoley/function/jobs/446149434

---
