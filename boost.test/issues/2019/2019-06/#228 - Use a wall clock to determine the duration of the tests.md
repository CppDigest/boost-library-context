# #228 - Use a wall clock to determine the duration of the tests [Open]

> Username: k15tfu  
> Created at: 2019-06-28 10:26:26 UTC  
> Updated at: 2019-12-04 00:54:29 UTC  
> Url: https://github.com/boostorg/test/issues/228  

Hi Raffi!  
  
We run tests on build agents and I noticed that the real duration of test cases (build server calculates it using test output) is much smaller than that measured by Boost.Test. Currently it uses boost::timer which is based on std::clock(). Probably usage of something like high_resolution_clock will be more preferable.  
  
Do you need help with PR?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-10-07 22:16:39 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-539227617  

Hi @k15tfu , would you please provide me with some hints? (measurement differences and platform). I do not believe the precision of `std::clock` is the issue there, and the time measurement reported are all wall time. I cannot really use `high_resolution_clock` as this part of the code should be compatible with C++03.  
  
Thanks!

---

## Comment 2

> Username: k15tfu  
> Created at: 2019-10-08 10:39:29 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-539455696  

@raffienficiaud , VMs powered by VMware ESXi on MacPro6,1. According to the log file the test lasted 34m:36s, but Boost reported 4039503ms (~1h07m).

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-10-09 17:47:06 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-540112298  

Thanks for the quick reply. Looking at the code I cannot see anything obviously wrong. I am just wondering: are your measurements reported by the Junit output?

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-10-09 18:07:54 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-540120882  

BTW: one measurement seems to be the double of the other, my feeling is that we are counting the same thing twice.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-10-09 20:48:33 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-540188295  

Would it be possible for you to test the branch `topic/GH-228-erroneous-tests-duration` ? Thanks.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-11-12 08:04:30 UTC  
> Url: https://github.com/boostorg/test/issues/228#issuecomment-552779935  

@k15tfu from my checks, I do not see the problem with the timing. The time for each tests is taken to be the wall clock time at the suite level, including some boost.test internal timing. This is why usually we have a test suite time that is greater than the accumulation of the its children test time.  
  
The branch I mentioned `topic/GH-228-erroneous-tests-duration` does not fix any issue.  
  
I need your input, thanks!
