# #178 Update tests to consistently pass on Windows (hopefully) [Merged]

> Username: austin-beer  
> Created at: 2017-10-12 03:45:25 UTC  
> Updated at: 2017-10-13 17:49:42 UTC  
> Merged at: 2017-10-13 17:35:52 UTC  
> Closed at: 2017-10-13 17:35:52 UTC  
> Url: https://github.com/boostorg/thread/pull/178  

Increased the time thresholds on a bunch of tests to be more forgiving so they pass consistently on Windows.  
  
Reduced the time thresholds on a bunch of tests that were TOO forgiving.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-12 16:44:01 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336195579  

I was wondering if we couldn't have less constraining duration only for windows, but we can do it once we have a green CI.

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-10-12 16:51:12 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336197572  

Yes, I think that would be a good idea. Do all the tests by chance share a common include file where we could set the threshold once and then use it in each of the tests?

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-10-12 17:33:14 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336210104  

Is it possible to cancel this AppVeyor job (https://ci.appveyor.com/project/viboes/thread/build/1.0.147-feature/timespec_clocks) since it's been superseded by this one (https://ci.appveyor.com/project/viboes/thread/build/1.0.148-feature/timespec_clocks)?

---

## Comment 4

> Username: viboes  
> Created_at: 2017-10-12 19:31:39 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336242056  

I've stopped the job.  
  
I wonder if we shoudln't print the difference. I have observed that sometimes we have less time than expected, no more. This can be due to time slicing; there is some time between the call to now and the call to wait.

---

## Comment 5

> Username: austin-beer  
> Created_at: 2017-10-12 19:41:16 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336244341  

All of the changes I've made allow the tests to take less time than expected without reporting errors. It's only when the tests take more than 200 milliseconds longer than expected that they report an error.  
  
Unfortunately I've seen that sometimes 200 milliseconds isn't long enough. I think the issue is that AppVeyor is using heavily-loaded VMs and so the tests sometimes run even slower than they do on a normal Windows system.

---

## Comment 6

> Username: austin-beer  
> Created_at: 2017-10-13 16:47:43 UTC  
> Url: https://github.com/boostorg/thread/pull/178#issuecomment-336506467  

The Travis CI failed due to a timing issue unrelated to these changes. My guess is it will pass if run again.  
  
The AppVeyor Visual Studio failures were due to issues unrelated to these changes. It seems like AppVeyor isn't always the most reliable CI platform.  
  
The AppVeyor MinGW failures are due to legitimate issues that we need to address, but that should probably be done in a separate PR, since they are unrelated to this PR.  
  
I'll add code to each test in this PR to set the timeout threshold based on whether we're running on Windows.

---
