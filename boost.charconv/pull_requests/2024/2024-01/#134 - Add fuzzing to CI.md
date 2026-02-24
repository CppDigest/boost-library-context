# #134 Add fuzzing to CI [Merged]

> Username: mborland  
> Created at: 2024-01-23 10:25:24 UTC  
> Updated at: 2024-01-26 10:12:49 UTC  
> Merged at: 2024-01-26 10:12:46 UTC  
> Closed at: 2024-01-26 10:12:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/134  

As required by @anarthal conditional acceptance. CC: @ckormanyos

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-23 11:07:30 UTC  
> Updated_at: 2024-01-26 09:57:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#issuecomment-1905811171  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
Attention: `2 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`b404767`)](https://app.codecov.io/gh/cppalliance/charconv/commit/b404767a5745791afe906d4a65e04d035bea1b6c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29% compared to head [(`4355914`)](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29%.  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/134/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #134   +/-   ##  
========================================  
  Coverage    88.29%   88.29%             
========================================  
  Files           56       56             
  Lines         7919     7919             
========================================  
  Hits          6992     6992             
  Misses         927      927             
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/compute\_float32.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDMyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `74.61% <89.47%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/134/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [b404767...4355914](https://app.codecov.io/gh/cppalliance/charconv/pull/134?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---

## Review 2 [Commented]

> Username: anarthal  
> Created_at: 2024-01-23 18:08:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/134#pullrequestreview-1839516611  

📁 fuzzing/Jamfile

```diff
  53 |+ 
  54 |+ 
  55 |+ # local initial-corpus = [ glob-tree-ex ../test : *.json ] ;
```

> Username: anarthal  
> Created_at: 2024-01-23 18:03:20 UTC  
> Updated_at: 2024-01-23 18:08:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1463728835  

This is missing an initial corpus (like Url did some time ago). It's an important thing to provide it so that the fuzzer is adequately guided. You may find [this page](https://docs.cppalliance.org/contributor-guide/testing/fuzzing.html) useful, which I wrote some time ago when I implemented fuzzing for MySQL.  
  
I suggest to extract the initial corpus from your unit tests.

> Username: mborland  
> Created_at: 2024-01-24 07:36:46 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1464452556  

What order of magnitude of values need to be in the seed corpus? Like 10s or 1'000s?

> Username: anarthal  
> Created_at: 2024-01-24 12:21:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1464836321  

It's more about variety than quantity - the more different cases you cover, the better. AFAIK libfuzzer instruments your code to measure coverage for each sample. Samples that don't trigger any extra coverage don't add value. I don't know exactly what metric they use to measure coverage. They recommend to add both well-formed and malformed samples.   
  
TL;DR: probably >10 and <1000

---

📁 fuzzing/Jamfile

```diff
  56 |+ 
  57 |+ 
  58 |+ local variants = from_chars_float from_chars_int ;
```

> Username: anarthal  
> Created_at: 2024-01-23 18:03:54 UTC  
> Updated_at: 2024-01-23 18:08:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1463729419  

You may also want to have a look at MySQL's Jamfile on fuzzing, which I think is shorter.


📁 fuzzing/fuzz_from_chars_float.cpp

```diff
  11 |+ extern "C" int LLVMFuzzerTestOneInput(const std::uint8_t* data, std::size_t size)
  12 |+ {
  13 |+     std::string s {reinterpret_cast<const char*>(data), size};
```

> Username: anarthal  
> Created_at: 2024-01-23 18:04:51 UTC  
> Updated_at: 2024-01-23 18:08:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1463730342  

Why do you need to copy this into a string? The faster the fuzzer, the better. And in real world, output is not null-terminated.

---

📁 fuzzing/fuzz_from_chars_float.cpp

```diff
  16 |+     {
  17 |+         double val;
  18 |+         boost::charconv::from_chars(s.c_str(), s.c_str() + s.size(), val);
```

> Username: anarthal  
> Created_at: 2024-01-23 18:06:06 UTC  
> Updated_at: 2024-01-23 18:08:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1463731937  

You may want to add fuzzers for the different options you have for doubles. For instance, you can make the first byte of input generated by LLVM be the precision you apply to `from_chars`.


📁 fuzzing/fuzz_from_chars_int.cpp

```diff
  15 |+     try
  16 |+     {
  17 |+         long val;
```

> Username: anarthal  
> Created_at: 2024-01-23 18:07:58 UTC  
> Updated_at: 2024-01-23 18:08:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#discussion_r1463735292  

You may also want to test with different int sizes.


---

## Comment 3

> Username: mborland  
> Created_at: 2024-01-25 09:19:29 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#issuecomment-1909726157  

@anarthal Let me know if there's anything else you wanted added to this; otherwise I think it's good to merge.

---

## Comment 4

> Username: anarthal  
> Created_at: 2024-01-25 16:51:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#issuecomment-1910601436  

Looks good to me.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-01-25 16:59:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#issuecomment-1910617449  

I don't see any actual fuzzing being performed in https://github.com/cppalliance/charconv/actions/runs/7651955730. Looks like the executables are just compiled and linked, but not run.

---

## Comment 6

> Username: mborland  
> Created_at: 2024-01-26 10:11:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/134#issuecomment-1911796102  

> I don't see any actual fuzzing being performed in https://github.com/cppalliance/charconv/actions/runs/7651955730. Looks like the executables are just compiled and linked, but not run.  
  
As usual you are correct and it has been fixed. Fuzzer found two issues which have both been addressed.

---
