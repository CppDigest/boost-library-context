# #133 - Timeout effect on Windows [Closed]

> Username: phlptp  
> Created at: 2018-03-18 15:29:44 UTC  
> Updated at: 2019-02-18 21:21:15 UTC  
> Closed at: 2019-02-18 21:20:50 UTC  
> Url: https://github.com/boostorg/test/issues/133  

I am running a set of tests on Windows Visual Studio 2017, using boost 1.66 in debug mode  
  
this is the result I get from a set of tests  
```  
Running 14 test cases...  
  
0%   10   20   30   40   50   60   70   80   90   100%  
|----|----|----|----|----|----|----|----|----|----|  
***************************************************  
  
*** Errors were detected in the test module "key_tests"; see standard output for details  
Press any key to continue . . .  
```  
this is a subset of a much larger test suite  
  
After digging down I finally figured out what was happening is that this test is a data test cases and has a timeout decorator and it was skipping the last few of the tests  
  
```  
BOOST_TEST_DECORATOR (*utf::timeout (5))  
BOOST_DATA_TEST_CASE (...  
```  
  
if report_level is set to detailed I get some additional explanation  
```  
Test module "key_tests" has failed with:  
  10 test cases out of 14 passed  
  4 test cases out of 14 skipped  
  120 assertions out of 120 passed  
```  
with some later indicators stating that the last few tests were skipped.  
I was expecting the decorator to apply to the individual tests vs the entire data test loop as well, but that may be my mistaken assumption.   
  
There are a couple issues with this. The first is that  
the [documentation](http://www.boost.org/doc/libs/master/libs/test/doc/html/boost_test/testing_tools/timeout.html)  
states the timeout decorator has no effect on windows, so this appears now wrong,  we have the timeouts in place as the test suites also run on Linux quite frequently.  
  
And second some failure messages in the standard output would be much appreciated as I had no idea what was going on when this first started showing up.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-03-20 21:54:30 UTC  
> Url: https://github.com/boostorg/test/issues/133#issuecomment-374770385  

Hi,  
  
Thanks for the report. There is currently a bug in our backlog: https://svn.boost.org/trac10/ticket/12095 that fires a failure as soon as a test is skipped. The timeout seems to be the same type of error.  
This happens only on Windows, is that correct?

---

## Comment 2

> Username: phlptp  
> Created at: 2018-03-20 22:15:59 UTC  
> Url: https://github.com/boostorg/test/issues/133#issuecomment-374775755  

I have only observed this issue on windows in debug mode, though on other systems the tests we have run will not take anywhere close to the amount of time for the timeout to trigger. In any case, it does appear to be the same type of error as the other ticket.  The fact that timeouts are doing something on Windows would seem to be a separate matter though.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-02 19:07:34 UTC  
> Url: https://github.com/boostorg/test/issues/133#issuecomment-417952914  

I think the problem here is that `BOOST_TEST_DECORATOR (*utf::timeout (5))` applies to the group of tests in the `BOOST_DATA_TEST_CASE`. In fact, each `BOOST_DATA_TEST_CASE` creates a test suite, and the `BOOST_TEST_DECORATOR` is being applied to the suite, and not to individual tests.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-02-13 16:36:55 UTC  
> Url: https://github.com/boostorg/test/issues/133#issuecomment-463270411  

I believe this is a duplicate of #203 and #138 as the decorators are not propagated properly from the parent test-suite. You can try the branch `topic/GH-138-decorator-dataset-testcase` and let me know if that works for you.
