# #1154 Add missing headers so that all headers compile independently [Merged]

> Username: vissarion  
> Created at: 2023-05-25 11:13:06 UTC  
> Updated at: 2023-07-25 19:56:18 UTC  
> Merged at: 2023-07-25 19:56:12 UTC  
> Closed at: 2023-07-25 19:56:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1154  

Fixes https://github.com/boostorg/geometry/issues/523  
  
Also adds a github actions script to test that all headers compile independently.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2023-05-30 08:26:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1154#issuecomment-1567993047  

Is this newly added test very heavy? All of the files in the library are compiled. AFAIR we decided not to run it automatically because it takes a lot of time. How long does it take vs minimal and documentation tests?

---

## Comment 2

> Username: vissarion  
> Created_at: 2023-05-30 11:42:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1154#issuecomment-1568287447  

Good point. I think it was not to run automatically because it was failing (see https://github.com/boostorg/geometry/pull/525#issuecomment-440507401). Also this PR does not change that, it still do not run automatically but only called by the new github action.   
  
Regarding performance I got the following timings (on my laptop with `clang-15`)   
| test | time  
|--|------|  
`b2 test/headers/ -j8` | 6m54.947s  
`b2 toolset=clang cxxstd=14,17,2a variant=debug,release address-model=32,64 test//minimal -j8` | 0m56.863s  
`b2 libs/geometry/doc/src/examples -j8` | 2m21.983s  
`cd boost/geometry/doc; b2 -j8` | 1m0.406s  
  
Note that for the minimal test the timing refers to testing  only 1 out of 14 compilers.   
  
So roughly speaking the headers test is the 1/3 of the current tests run on actions (i.e. minimal & documentation). It is somehow heavy I agree.   
  
Alternatively, we can enable it only for push and not for PRs or even add it to circleCI (where the percentage over the total test time would be much smaller).

---

## Comment 3

> Username: vissarion  
> Created_at: 2023-06-01 13:03:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1154#issuecomment-1572015972  

@awulkiew the new test has picked up by github actions so now you can also check the times in the server; i.e. 20m for `headers` vs. 4-11m for each `minimal` test.

---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2023-07-02 09:00:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1154#pullrequestreview-1509513615  

Thanks! I didn't review all changes in detail, but in general it looks good to me.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2023-07-21 21:31:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1154#issuecomment-1646257913  

Right, so it doesn't take that long vs all of the tests, since it's done only once. Thanks!

---
