# #174 feature/pr165 refactor [Merged]

> Username: joaquintides  
> Created at: 2022-12-05 19:26:54 UTC  
> Updated at: 2024-08-17 16:49:17 UTC  
> Merged at: 2022-12-15 18:45:10 UTC  
> Closed at: 2022-12-15 18:45:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/174  

Refactoring of [PR165](https://github.com/boostorg/unordered/pull/165) introducing a [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md)-inspired `narrow_cast` that internally takes care of making VS run-time checks happy.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-12-05 19:36:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1338044092  

What's the contract/specification of narrow_cast?  
  
All that ifdefing on rvalue references doesn't seem necessary to me.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-12-06 03:47:33 UTC  
> Updated_at: 2022-12-14 18:43:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1338700362  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#174](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d5dac7c) into [develop](https://codecov.io/gh/boostorg/unordered/commit/eb33ad3e3f416ae94dfa216b06509ebb4b9a67eb?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb33ad3) will **increase** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head d5dac7c differs from pull request most recent head f0037d3. Consider uploading reports for the commit f0037d3 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/174/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #174      +/-   ##  
===========================================  
+ Coverage    97.62%   97.66%   +0.03%       
===========================================  
  Files           83       85       +2       
  Lines        12263    12072     -191       
===========================================  
- Hits         11972    11790     -182       
+ Misses         291      282       -9       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `97.93% <100.00%> (-0.70%)` | :arrow_down: |  
| [include/boost/unordered/detail/narrow\_cast.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL25hcnJvd19jYXN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/prime\_fmod.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3ByaW1lX2Ztb2QuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/narrow\_cast\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbmFycm93X2Nhc3RfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/objects/test.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL3Rlc3QuaHBw) | `94.33% <0.00%> (-1.44%)` | :arrow_down: |  
| [test/exception/assign\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vYXNzaWduX2V4Y2VwdGlvbl90ZXN0cy5jcHA=) | `95.16% <0.00%> (-0.56%)` | :arrow_down: |  
| [test/objects/exception.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL2V4Y2VwdGlvbi5ocHA=) | `97.16% <0.00%> (-0.08%)` | :arrow_down: |  
| [test/helpers/invariants.hpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL2ludmFyaWFudHMuaHBw) | `75.00% <0.00%> (ø)` | |  
| [test/unordered/copy\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29weV90ZXN0cy5jcHA=) | `100.00% <0.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/unordered/pull/174/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eb33ad3...f0037d3](https://codecov.io/gh/boostorg/unordered/pull/174?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: joaquintides  
> Created_at: 2022-12-06 09:19:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339016030  

I just copied [this implementation](https://github.com/microsoft/GSL/blob/main/include/gsl/util#L93-L101) from GSL. Chris is also in favor of simplifying the signature, and has run Godbolt experiments showing that codegen is the same, so [here](https://github.com/boostorg/unordered/pull/174/commits/100598e285b3e7269bdd63079ee351f15211560c) we go.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-12-06 09:25:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339023436  

Yeah, OK, but what's the specification of narrow_cast? What are the requirements on From and To?

---

## Comment 5

> Username: joaquintides  
> Created_at: 2022-12-06 09:28:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339026873  

They must be integral types with `sizeof(From)>=sizeof(To)`.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-12-06 09:30:24 UTC  
> Updated_at: 2022-12-06 09:41:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339029221  

Can either of them be signed? Can they differ in signedness? (sizeof isn't quite the right test, it's more correct to test `numeric_limits<T>::digits`, but I get what you mean.)

---

## Comment 7

> Username: joaquintides  
> Created_at: 2022-12-06 09:35:41 UTC  
> Updated_at: 2022-12-06 09:36:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339035358  

They can differ in signedness, though we're only using it with same-signedness integral types.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-12-06 09:41:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339041957  

In that case, we need the correct static asserts in narrow_cast, and a test for it exercising all the possibilities. (Visual inspection isn't enough for me to be sure that it works correctly for all the various combinations of signed/unsigned types.)

---

## Comment 9

> Username: joaquintides  
> Created_at: 2022-12-06 10:50:50 UTC  
> Updated_at: 2022-12-06 12:43:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339135529  

I've just expanded the static asserts as indicated. As for testing, the following:  
```cpp  
#include <boost/config.hpp>  
#include <boost/static_assert.hpp>  
#include <boost/type_traits/is_integral.hpp>  
  
template<typename To,typename From>  
BOOST_CONSTEXPR To narrow_cast(From x) BOOST_NOEXCEPT  
{  
  BOOST_STATIC_ASSERT_MSG(  
    boost::is_integral<From>::value&&  
    boost::is_integral<To>::value&&  
    sizeof(From)>=sizeof(To),  
    "From and To must be integral types with sizeof(From)>=sizeof(To)");  
  
  return static_cast<To>(  
    x  
    &static_cast<To>(~static_cast<To>(0))  
  );  
}  
  
#include <cassert>  
#include <cstdint>  
#include <limits>  
  
template<typename From,typename To>  
void test_narrow_cast()  
{  
  for(From n=std::numeric_limits<From>::min();  
      n<std::numeric_limits<From>::max();  
      ++n){  
    assert(narrow_cast<To>(n)==static_cast<To>(n));  
  }  
}  
  
int main()  
{  
  test_narrow_cast<signed int,unsigned char>();  
  test_narrow_cast<unsigned int,signed char>();  
  test_narrow_cast<std::uint64_t,std::uint32_t>();  
  test_narrow_cast<std::uint64_t,signed short>();  
}  
```  
[works fine](http://coliru.stacked-crooked.com/a/91934afe689b8b12). Note that the testing code exercises the `__MSVC_RUNTIME_CHECKS` branch, which is the contentious one (otherwise, `narrow_cast` is just a `static_cast`). I don't think this testing needs to be included as part of Boost.Unordered, as the component is an internal one, and correctness surely can be formally proved by someone fluent in standardese.

---

## Comment 10

> Username: cmazakas  
> Created_at: 2022-12-06 15:56:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339592660  

I wouldn't mind adding the test file. It'd be simple enough and we'd have some good coverage over it. We don't even need to do exhaustive checks, just as many different edge-cases as we can think of.

---

## Comment 11

> Username: pdimov  
> Created_at: 2022-12-06 16:05:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1339605685  

The problem with doing the test like this (checking against `static_cast`) is that it will do nothing without `/RTCc`, and abort with `/RTCc`. I would just check a few reference values, e.g. `BOOST_TEST_EQ( narrow_cast<signed char>( -1 ), (signed char)-1 );` and similar.  
  
It's better if the static asserts are separated; this way there's information as to which condition isn't met. While some recent compilers actually decompose the condition and tell you which part failed, far from all do. (And there would be no need for _MSG in that case, because it would just repeat the condition, except in English.)

---

## Comment 12

> Username: cmazakas  
> Created_at: 2022-12-14 18:45:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1351956030  

I think the tests are good. I like how narrow_cast() came out and how it looks in the code so I'm okay with all the changes. Let me know what you guys think!

---

## Comment 13

> Username: joaquintides  
> Created_at: 2022-12-15 08:11:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/174#issuecomment-1352696961  

Looks all good to me. Thans for testing this thoroughly,

---
