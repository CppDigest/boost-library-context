# #256 test pr benchmarks [Closed]

> Username: sdarwin  
> Created at: 2020-09-03 14:54:58 UTC  
> Updated at: 2020-09-09 23:10:24 UTC  
> Closed at: 2020-09-09 23:10:24 UTC  
> Url: https://github.com/boostorg/json/pull/256  

Don't merge.  
Testing the functionality of benchmark computation on pull requests. Processing takes an hour or so.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-03 17:53:23 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-686653239  

Benchmark test results: [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html)

---

## Comment 2

> Username: sdarwin  
> Created_at: 2020-09-03 18:08:10 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-686661052  

Looks good. Closing PR.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-09-03 18:31:15 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-686672232  

I thought the image would be inlined in the message

---

## Comment 4

> Username: sdarwin  
> Created_at: 2020-09-03 18:53:30 UTC  
> Updated_at: 2020-09-03 18:53:47 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-686694068  

> I thought the image would be inlined in the message  
  
I will see what can be done about showing the images in a smaller window... Re-opening the issue.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-09-04 20:18:31 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-687361778  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html)

---

## Comment 6

> Username: sdkrystian  
> Created_at: 2020-09-05 22:05:13 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-687668958  

Hm, the wild variance in the results might be my fault. For gcc it might be comparing against clang develop.... I'll check

---

## Comment 7

> Username: sdarwin  
> Created_at: 2020-09-05 22:23:22 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-687670616  

> Hm, the wild variance  
  
I was also surprised by that.  But it wasn't an exact comparison because the develop branch changed since the pull request was submitted.   Further testing of the same commit with itself shows less variance.

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-09-08 06:21:45 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-688644334  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2020-09-09 21:44:43 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-689838312  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest-condensed-49040a1.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/256/pullrequest.html)

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-09-09 21:57:23 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-689843401  

is this pull request actionable?

---

## Comment 11

> Username: sdarwin  
> Created_at: 2020-09-09 22:27:56 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-689855518  

> is this pull request actionable?  
  
It has become actionable, because it seems the build job is occasionally triggering for no reason.  The benchmarks ran, however there wasn't a new commit.  Investigating...

---

## Comment 12

> Username: sdarwin  
> Created_at: 2020-09-09 23:10:24 UTC  
> Url: https://github.com/boostorg/json/pull/256#issuecomment-689870631  

Latest theory:  Although no new commits have been submitted to the PR, the "target" or "base" of the PR (which is the develop branch) has changed in the meantime, and that is causing the plugin to sometimes trigger rebuilds.   
  
@sdkrystian  -  benchmark computations have been revised to compare against the point of divergence from the develop branch instead of the newest commit.

---
