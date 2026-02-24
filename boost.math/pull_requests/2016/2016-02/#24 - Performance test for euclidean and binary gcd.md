# #24 Performance test for euclidean and binary gcd. [Merged]

> Username: jeremy-murphy  
> Created at: 2016-02-09 11:18:04 UTC  
> Updated at: 2016-03-09 18:53:01 UTC  
> Merged at: 2016-03-09 18:53:01 UTC  
> Closed at: 2016-03-09 18:53:01 UTC  
> Url: https://github.com/boostorg/math/pull/24  

This currently includes two other pending pull requests, so the file of interest is `test_gcd.cpp`.  
  
At first I was envisioning testing the cross product of different data types (unsigned, polynomial, etc) and functions (gcd_euclidean and gcd_binary), but the polynomial stuff isn't ready and it started to get very complicated. So this just tests the two gcd algorithms on unsigned.  
  
The existing `exec_timed_test` didn't seem to quite fit for gcd so I made a simplified version here.  
  
I don't quite understand what the latter parameters for `report_execution_time` are, so I have just provided some calculated values that vaguely seem to fit.  
  
Do I need to include the resulting `performance_tables.qbk` file or is that generated automatically by some standard system for measuring performance?

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-02-09 13:07:23 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-181856095  

I'll defer this until we have #23 merged.  
  
performance_tables.qbk is regenerated when all the performance tests are run, so no need to include it in the commit just yet.  I'll double check the args to report_execution_time and the tables generated later.  
  
Thanks for this!

---

## Comment 2

> Username: pabristow  
> Created_at: 2016-02-09 14:28:15 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-181887463  

Are there any changes to documentation from these changes?  
Would users benefit from an example of use of initilizer list ?  Or just a reference to the test?

---

## Comment 3

> Username: pabristow  
> Created_at: 2016-02-09 14:28:46 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-181887779  

Sorry - fat thumb - didn't mean to close.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-02-09 21:48:38 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-182092515  

I think initializer_list usage is quite intuitive, so I'll put in something minimal into the documentation that I otherwise overlooked. :)

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-02-10 01:37:29 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-182162028  

What kind of precision do you expect from a performance test? I see a 10-15% fluctuation in measurement, which does not inspire me with confidence. I guess should use a range of input values, too.

---

## Comment 6

> Username: pabristow  
> Created_at: 2016-02-10 12:19:00 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-182342803  

From: Jeremy W. Murphy [mailto:notifications@github.com]   
Sent: 10 February 2016 01:38  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] Work-in-progress: performance test for euclidean and binary gcd. (#24)  
  
What kind of precision do you expect from a performance test?  
  
Not a lot L  
  
I see a 10-15% fluctuation in measurement, which does not inspire me with confidence.   
  
I fear that it is common.  
  
I guess should use a range of input values, too.  
  
Maybe – though I found that it just added confusion LL  
  
Caching seemed to be a major source of potentially mis-leading results.  
  
The tick timer on MSVC is very much bigger than on Linux, I think I remember?  
  
Sorry that isn’t much help…  
  
Paul

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2016-02-10 18:04:06 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-182506016  

On 10/02/2016 01:37, Jeremy W. Murphy wrote:  
  
> What kind of precision do you expect from a performance test? I see a   
> 10-15% fluctuation in measurement, which does not inspire me with   
> confidence. I guess should use a range of input values, too.  
  
That doesn't surprise me.  However, note that:  
- Compilers are often way too clever here - executing the same statement   
  over and over often gets optimised away to just one call.  
- Any result that measures a short duration typically has a lot of noise   
- you seem to require about a second of execution time to get consistent   
  results.  
  
John.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2016-03-02 02:12:03 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-191019444  

OK, I'm back from a trip overseas, so I hope to finish this up soon.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-03-09 02:02:08 UTC  
> Url: https://github.com/boostorg/math/pull/24#issuecomment-194066741  

This might be ready to go now: any thoughts?

---
