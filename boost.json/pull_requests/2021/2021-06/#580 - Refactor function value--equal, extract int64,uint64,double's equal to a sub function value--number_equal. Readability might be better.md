# #580 Refactor function value::equal, extract int64,uint64,double's equal to a sub function value::number_equal. Readability might be better. [Closed]

> Username: ninghejun  
> Created at: 2021-06-18 10:12:55 UTC  
> Updated at: 2021-06-21 07:08:03 UTC  
> Closed at: 2021-06-21 07:08:03 UTC  
> Url: https://github.com/boostorg/json/pull/580  

Refactor function value::equal, extract int64,uint64,double's equal to a sub function value::number_equal. Readability might be better.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-06-18 11:24:12 UTC  
> Url: https://github.com/boostorg/json/pull/580#issuecomment-863967798  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/580/pullrequest-condensed-9d5bb2d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/580/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/580/pullrequest.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-06-18 15:54:57 UTC  
> Url: https://github.com/boostorg/json/pull/580#issuecomment-864133159  

What problem is this trying to solve? It seems we are adding a new public function, `number_equal`, which has undefined behavior for some inputs.

---

## Comment 3

> Username: ninghejun  
> Created_at: 2021-06-21 02:47:26 UTC  
> Updated_at: 2021-06-21 02:52:52 UTC  
> Url: https://github.com/boostorg/json/pull/580#issuecomment-864683633  

> What problem is this trying to solve? It seems we are adding a new public function, `number_equal`, which has undefined behavior for some inputs.  
  
Switch nesting in the equal function is not readable enough.  So the `equal` function is too long. This submission is an attempt to improve readability.  
  
`number_equal` is private, only called by `equal`.  the inputs have been checked by `is_number()` in `equal`.  
If we call `number_equal` separately, we get undefined behavior.  It was my fault. I can add parameter checks to the function `number_equal` later.  
  
Is this submission worth it?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-06-21 03:07:51 UTC  
> Url: https://github.com/boostorg/json/pull/580#issuecomment-864690193  

hmm its pretty readable as it is. There are other considerations here such as code coverage, undefined behavior, And It seems that this change fails tests. Given that the code works and is already pretty understandable, I'm not sure changing it is an improvement. But thank you for taking a look.

---

## Comment 5

> Username: ninghejun  
> Created_at: 2021-06-21 07:08:03 UTC  
> Url: https://github.com/boostorg/json/pull/580#issuecomment-864785689  

Yes.  
  
I saw 'Enumeration values not handled in switch' in the test result.  
  
These test case failures can be fixed, but result in poorer branch coverage.  
  
Then this change is meaningless.  
  
So this pull request can be closed.

---
