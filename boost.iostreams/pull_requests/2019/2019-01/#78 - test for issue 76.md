# #78 test for issue 76 [Open]

> Username: lvoege  
> Created at: 2019-01-15 20:13:37 UTC  
> Updated at: 2025-10-12 09:02:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/78  

on a theoretical 64bit Windows machine where allocating 4+GB fails the test would presumably exception out with a bad_alloc. that would make sense as such a theoretical Windows machine would be unable to properly run this test or verify that a fix fixed it.

---

## Comment 1

> Username: mclow  
> Created_at: 2019-01-15 21:00:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/78#issuecomment-454549259  

So what happens when the call to `new` (allocating 4+GB) fails?

---

## Comment 2

> Username: mclow  
> Created_at: 2019-01-15 21:32:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/78#issuecomment-454559480  

Also, do you have to handle the case when the disk fills up from the write?

---

## Comment 3

> Username: lvoege  
> Created_at: 2019-01-25 20:02:09 UTC  
> Updated_at: 2019-01-25 20:02:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/78#issuecomment-457707529  

I would say not. when I try the test with a full disk I get an exception and the test fails. as with not enough memory I think it's good that a test that can't usefully verify a fix on some machine fails.  
  
I tried eating those exceptions and at least BOOST_WARN_MESSAGE()ing about it, but I don't actually see those warnings show up anywhere then, so then the test just silently passes when not enough memory or disk. that seems bad.  
  
```  
    {  
        temp_file             temp;  
        file_descriptor_sink  file(temp.name(), BOOST_IOS::trunc);  
        std::streamsize len = static_cast<std::streamsize>(4ULL * 1024 * 1024 * 1024 + 1);  
        try {  
            std::unique_ptr<char[]> buf(new char[len]);  
            memset(&buf[0], 0, len);  
            try {  
                std::streamsize written = file.write(&buf[0], len);  
                std::streamsize pos = file.seek(0, BOOST_IOS::cur);  
                BOOST_CHECK_MESSAGE(  
                    pos == written,  
                    "silent short write"  
                );  
            } catch (...) {  
                if (GetLastError() != ERROR_DISK_FULL)  
                    throw;  
                BOOST_WARN_MESSAGE(false, "Disk full");  
            }  
        } catch (std::bad_alloc &) {  
            BOOST_WARN_MESSAGE(false, "Not enough memory to run test");  
        }  
        file.close();  
        BOOST_CHECK(!file.is_open());  
    }  
  
```

---

## Review 4 [Changes requested]

> Username: jeking3  
> Created_at: 2019-05-03 16:40:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iostreams/pull/78#pullrequestreview-233564531  

Okay, we identified something with a test.  Now we need to fix it. :)

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2019-05-13 15:21:19 UTC  
> Updated_at: 2025-10-12 09:02:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/78#issuecomment-491867650  

## [Codecov](https://app.codecov.io/gh/boostorg/iostreams/pull/78?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 69.04%. Comparing base ([`50b4f00`](https://app.codecov.io/gh/boostorg/iostreams/commit/50b4f003e5205db1243e4d2469e6f795fb57dc45?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`c006eeb`](https://app.codecov.io/gh/boostorg/iostreams/commit/c006eeb44cc24b3621fe960754df9bc79aac41be?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 84 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #78   +/-   ##  
========================================  
  Coverage    69.04%   69.04%             
========================================  
  Files           80       80             
  Lines         3437     3437             
  Branches      1025     1025             
========================================  
  Hits          2373     2373             
  Misses         451      451             
  Partials       613      613             
```  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/iostreams/pull/78?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Have feedback on the report? [Share it here](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
