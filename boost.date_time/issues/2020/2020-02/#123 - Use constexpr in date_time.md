# #123 - Use constexpr in date_time [Closed]

> Username: rogerorr  
> Created at: 2020-02-10 13:32:21 UTC  
> Updated at: 2020-07-22 15:30:34 UTC  
> Closed at: 2020-07-22 15:30:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/123  

Certain of the methods in date_time would benefit from being marked `constexpr`.  
  
For example, being able to use `time_duration::ticks_per_second()` in a constexpr context would be useful. This returns `rep_type::res_adjust()` which in turn seems to come from the `resolution_adjust` NTTP from `time_resolution_traits` .

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-02-11 05:38:26 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-584485004  

Thanks Roger -- will look at this.  Just to fill in from the discussion we had in Prague, you've observed a significant performance benefit from having the adjustment parameters calculated at compile time which simplifies the run time calculations.

---

## Comment 2

> Username: rogerorr  
> Created at: 2020-02-11 06:34:56 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-584495755  

Correct. We had code which needed to divide by the ratio between 1e7 and `ticks_per_second`.  
This generated a full divide operation - the compiler did not optimise through the two calls. Marking the ratio and the two calls mentioned above as `constexpr` allows the compiler to divide by a compile-time constant which is optimised to a multiply using the standard transformation in such cases.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-03-04 15:26:53 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-594599801  

The initial set of library changes is now complete on branch dt_constexpr -- compiles and runs all unit tests successfully with gcc9.2 with c++ versions: 98, 11, 14, 17, 2a.  Now waiting for the CI to test against all the compilers.  Also should add a test that explicitly exercises the constexpr to see what has been achieved.

---

## Comment 4

> Username: JeffGarland  
> Created at: 2020-03-16 15:07:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-599588230  

So @rogerorr -- on the latest develop branch push I've confirmed that the core is now supporting constexpr including the traits you mentioned in the issue.  I will eventually write more tests, but hoping to get this latest branch in the next release -- waiting for the test bots to churn before merging to master.  From test/testmisc.cpp.  This requires c++14 mode to work.  
  
    constexpr microseconds ms(1000);  
    static_assert(ms.is_special()  == false, "constexpr duration is_special");  
    static_assert(ms.is_positive() == true,  "constexpr duration is_positive");  
    static_assert(ms.is_negative() == false, "constexpr duration is_negative");  
    static_assert(ms.total_microseconds() == 1000, "constexpr total_microseconds");

---

## Comment 5

> Username: rogerorr  
> Created at: 2020-03-26 15:54:33 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-604511594  

Hello @JeffGarland -- I haven't been in a position to check this until now; but I can confirm the `constexpr` change is working successfully for the use case I presented.   
Thank you!

---

## Comment 6

> Username: JeffGarland  
> Created at: 2020-03-26 16:40:08 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-604537360  

You're welcome.  The changes have been pushed to master so this will be in the 1.73 release.  I'm leaving this open until all the docs and such are finished

---

## Comment 7

> Username: rogerorr  
> Created at: 2020-05-04 13:31:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-623465226  

The changes in 1.73.0 have made measurable improvements to some of our performance benchmarks -- thanks for this!  
  
I'm looking at making some more of our constants `constexpr` and there seems to be a missing `BOOST_CXX14_CONSTEXPR` on the `assign` method of `constrained_value` which prevents the constructors (which call assign) being constexpr. Happy to raise a fresh ticket if you'd like, or leave this here -- whichever is best for you :)

---

## Comment 8

> Username: JeffGarland  
> Created at: 2020-05-04 16:03:54 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-623553701  

Glad it was useful, and thanks -- indeed assign got missed.  I'll just keep working it under this ticket as I wanted to expand the tests a bit anyway to make sure it was covered.

---

## Comment 9

> Username: rogerorr  
> Created at: 2020-05-04 18:06:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-623618132  

Sounds fine. Good luck!

---

## Comment 10

> Username: JeffGarland  
> Created at: 2020-07-22 15:30:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/123#issuecomment-662521694  

Assign and new tests are all complete and will be part of the 1.74 release.  This is now complete.
