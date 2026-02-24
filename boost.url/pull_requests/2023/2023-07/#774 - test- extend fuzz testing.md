# #774 test: extend fuzz testing [Merged]

> Username: alandefreitas  
> Created at: 2023-07-25 00:03:03 UTC  
> Updated at: 2024-02-21 00:55:01 UTC  
> Merged at: 2023-07-27 18:47:54 UTC  
> Closed at: 2023-07-27 18:47:54 UTC  
> Url: https://github.com/boostorg/url/pull/774  

fix #763

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-07-25 00:17:28 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648788837  

@anarthal... so this is what I got...  
  
I was looking at this and trying to fix everything in both b2 and cmake. Until I realized the real problem is I simply don't have the skills to do that in b2 and that's why it would suck forever. So I put the fuzz target as part of the cmake workflow in CI and called it a day.   
  
We also had a fuzz test scheduled every X hours but I realized that was statistically stupid when you compare the cost of building the library with the cost of running these tests and other workflows.  
  
The problem with b2 is I know exactly what I have to achieve and how to achieve it but I can't replicate the cmake logic in b2, replicating from other projects doesn't work in this case, and doing it makes me 99% less productive because I don't know how to do things, the web doesn't help a lot, and asking other people on slack for every line of code takes a long time. It's an awful corner. It's OK when the library has a format to exactly like some other library but any other case is completely thankless.  
  
(CI is stuck waiting for runners so this PR might have changed when you read this message)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-07-25 00:26:11 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648794240  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-07-25 00:33:38 UTC  
> Updated_at: 2023-07-27 17:35:56 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648798688  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#774](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (96f7a7b) into [develop](https://app.codecov.io/gh/boostorg/url/commit/33cfb939d06f6ad9130ac185434dc63f94e9d990?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (33cfb93) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/774/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #774   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [33cfb93...96f7a7b](https://app.codecov.io/gh/boostorg/url/pull/774?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-25 00:42:16 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648803589  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-07-25 01:20:59 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648827047  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-07-25 01:43:19 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648840389  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-07-25 02:16:06 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1648879054  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-07-25 14:00:59 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1649904970  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-07-25 14:37:21 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1649969547  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-07-25 15:28:25 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1650060355  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-07-25 16:21:20 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1650150295  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: alandefreitas  
> Created_at: 2023-07-25 17:38:03 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1650260927  

@anarthal... just fixed the usual annoying small problems that happen in CI. It should be looking good now.

---

## Review 13 [Commented]

> Username: anarthal  
> Created_at: 2023-07-27 10:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/774#pullrequestreview-1549465175  

📁 test/fuzz/absolute_uri.cpp

```diff
  22 |+         core::string_view s{reinterpret_cast<
  23 |+             const char*>(data), size};
  24 |+         return parse_absolute_uri(s).has_value();
```

> Username: anarthal  
> Created_at: 2023-07-27 09:54:45 UTC  
> Updated_at: 2023-07-27 10:02:57 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276041141  

If this function throwing an exception represents a bug (which I think it does), then remove the try/catch

> Username: alandefreitas  
> Created_at: 2023-07-27 12:08:06 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276185156  

Oh... That's a good point. Any exceptions not handled by the parsing function are indeed a bug.


📁 test/fuzz/CMakeLists.txt

```diff
  38 |+     add_custom_target(untar_corpus
  39 |+         COMMAND ${CMAKE_COMMAND} -E echo "No corpus. Create ${CMAKE_CURRENT_SOURCE_DIR}/corpus."
  40 |+         COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_CURRENT_SOURCE_DIR}/corpus
```

> Username: anarthal  
> Created_at: 2023-07-27 09:56:22 UTC  
> Updated_at: 2023-07-27 10:02:57 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276043193  

Modifying something inside SOURCE_DIR sounds a bad practice

> Username: alandefreitas  
> Created_at: 2023-07-27 12:12:11 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276189279  

Yes. I got this pattern from the b2 scripts and thought this would help me keep them compatible. But it doesn't make sense now that I removed it from b2.   
  
Moving the corpus to another dir for the corpus is easy because I'm already in CMake and can use CMAKE_CURRENT_SOURCE_DIR. But I'm sure where to put the initial corpus.tar from the cache before running cmake. What do you recommend?

> Username: alandefreitas  
> Created_at: 2023-07-27 12:16:21 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276193471  

Do the initial seeds in `test/fuzz/seeds` look OK?

> Username: anarthal  
> Created_at: 2023-07-27 13:48:17 UTC  
> Updated_at: 2023-07-27 13:48:18 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276309975  

Yes they do. I store them as a single, compressed file bc I've got the feeling it's faster to checkout (mine are binary files tho).  
  
The best way to check whether they're okay is measuring coverage though. You can follow [this](https://docs.cppalliance.org/contributor-guide/testing/fuzzing.html#_verifying_the_effectiveness_of_your_fuzzer) to do it.

> Username: alandefreitas  
> Created_at: 2023-07-27 13:51:33 UTC  
> Updated_at: 2023-07-27 13:51:34 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276314346  

Oh! So site-docs is already being useful. :)

> Username: alandefreitas  
> Created_at: 2023-07-27 15:32:20 UTC  
> Updated_at: 2023-07-27 15:32:28 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276465024  

OK. I found a solution to this.   
  
The corpus goes to `${CMAKE_CURRENT_BINARY_DIR}/corpus` and `corpus.tar` comes from an option `BOOST_URL_FUZZER_CORPUS_PATH` defaulting to `${CMAKE_CURRENT_BINARY_DIR}/corpus.tar`.   
This means it can locally keep and reuse a corpus.tar in the build dir and in CI it downloads the cached corpus somewhere and sets the option.  
  
I also put the seeds in a single archive that's extracted to `${CMAKE_CURRENT_BINARY_DIR}/seeds` when running the target.

---

📁 test/fuzz/CMakeLists.txt

```diff
  56 |+     set(SOURCE_FILES ${ARGN})
  57 |+     add_executable(fuzzer_${NAME} ${SOURCE_FILES})
  58 |+     target_link_libraries(fuzzer_${NAME} PRIVATE Boost::url)
```

> Username: anarthal  
> Created_at: 2023-07-27 10:02:32 UTC  
> Updated_at: 2023-07-27 10:02:57 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276053678  

Is it enough to only build the executable with fuzzing flags? Don't you need to build the library with them, too? I use header-only mode for fuzzers so I don't have an answer to the question.

> Username: alandefreitas  
> Created_at: 2023-07-27 12:15:18 UTC  
> Url: https://github.com/boostorg/url/pull/774#discussion_r1276192410  

Good point. Yes. I think that's "OK" but diagnostics are worse. I'll fix that.


---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-07-27 13:22:50 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1653622241  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-07-27 14:15:00 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1653712762  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-07-27 15:25:25 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1653846570  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-07-27 17:31:34 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1654079822  

GCOVR code coverage report [https://774.url.prtest2.cppalliance.org/gcovr/index.html](https://774.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://774.url.prtest2.cppalliance.org/genhtml/index.html](https://774.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://774.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 18

> Username: anarthal  
> Created_at: 2023-07-27 17:37:14 UTC  
> Url: https://github.com/boostorg/url/pull/774#issuecomment-1654092431  

I use  
/tmp/seedcorpus/$fuzzer  
/tmp/corpus/$fuzzer  
/tmp/mincorpus/$fuzzer  
  
All the extraction and compression is done by the CI script, not cmake/b2, so the b2 script is kept very simple.

---
