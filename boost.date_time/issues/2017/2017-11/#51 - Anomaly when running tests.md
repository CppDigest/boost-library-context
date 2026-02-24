# #51 - Anomaly when running tests [Closed]

> Username: eldiener  
> Created at: 2017-11-10 07:37:05 UTC  
> Updated at: 2017-12-22 12:57:31 UTC  
> Closed at: 2017-12-22 12:57:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/51  

I am seeing a strange anomaly when running date_time tests, both locally and in the regression tests. The xml serialization tests show all tests passing and yet the tests return a non-0 code indicating run-time failure. In looking at the code in testfrmwk.hpp, which prints the total results of the tests and then returns a difference between the tests run and the tests passed, the total results printout is predicated on whether tests passed != tests run or not, which shows they are equal, but the subsequent return, which is tests run - tests passed shows 3 more tests run than passed ! This suggests that something is getting overwritten in memory between one line and the next, but I have no idea why this is happening. If anybody else can trace this down it would be very helpful, since the regression tests show these tests failing across the board, where they are actually succeeding according to the test results.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-12-21 14:12:50 UTC  
> Url: https://github.com/boostorg/date_time/issues/51#issuecomment-353360517  

Do you still see this with Boost 1.66.0?  There were a number of changes in Boost.Serialization, for example the singleton correctness, which might have played a part in what you describe (just a guess).

---

## Comment 2

> Username: eldiener  
> Created at: 2017-12-21 15:51:09 UTC  
> Url: https://github.com/boostorg/date_time/issues/51#issuecomment-353385216  

This was fixed by a change Peter Dimov made.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-12-21 16:11:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/51#issuecomment-353390304  

Nice, sounds like the issue can be closed.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-12-22 12:54:03 UTC  
> Url: https://github.com/boostorg/date_time/issues/51#issuecomment-353592982  

(might want to close this)
