# #196 - junit report: test error is also reported as failure [Closed]

> Username: mistafunk  
> Created at: 2019-01-21 17:55:46 UTC  
> Updated at: 2019-03-01 18:30:53 UTC  
> Closed at: 2019-03-01 18:30:53 UTC  
> Url: https://github.com/boostorg/test/issues/196  

Consider this snippet:  
  
```  
#define BOOST_TEST_MODULE Test1  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(testexception)  
{  
 throw 42;  
}  
```  
  
Compiling and running above sample yields:  
  
```  
<?xml version="1.0" encoding="UTF-8"?>  
<testsuite tests="0" skipped="0" errors="1" failures="1" id="0" name="Test1" time="0.000137">  
<testcase assertions="1" name="testexception" time="0.000137">  
```  
  
Now, this is not so great. JUnit would report this as:  
  
```  
<?xml version="1.0" encoding="UTF-8"?>  
<testsuite tests="0" skipped="0" errors="1" failures="0" id="0" name="Test1" time="0.000137">  
<testcase assertions="1" name="testexception" time="0.000137">  
```  
  
Because according to JUnit a test either succeeds or fails (one or more assertion failures) or produces an error (unexpected failure). Boost on the other hand thinks that an error is also a failure.  
  
Reproduced with Boost 1.66.0

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-08 13:22:22 UTC  
> Url: https://github.com/boostorg/test/issues/196#issuecomment-461800861  

If I understand well, as soon as there is an error, there is no failure? Can it happen that there are several errors?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-11 00:30:38 UTC  
> Url: https://github.com/boostorg/test/issues/196#issuecomment-462196298  

I think I fixed this in branch `topic/GH-196-junit-abort-fail-not-same`. It would be good if you have a look to tell me if I understood the difference between `errors` and `failures` in the JUnit sense.  
  
Thanks!

---

## Comment 3

> Username: mistafunk  
> Created at: 2019-02-15 07:50:56 UTC  
> Updated at: 2019-02-15 08:04:28 UTC  
> Url: https://github.com/boostorg/test/issues/196#issuecomment-463941533  

@raffienficiaud i understand junit report semantics like this:  
* failure: a test case with one or more failed assert-like statements (this is a bit contested, there are other frameworks which count each assertion failure)  
* error: a runtime error in the test case (i.e. bad test case implementation)  
  
to your questions:  
* technically, there could be first a failed assertion and then an unexpected/unhandled exception or segfault within a test case. junit report ignores such assertion failures.  
* while there could be multiple implementation issues in a test case, the first one is usually severe enough and that's the error.

---

## Comment 4

> Username: mistafunk  
> Created at: 2019-02-15 08:04:39 UTC  
> Url: https://github.com/boostorg/test/issues/196#issuecomment-463944822  

@raffienficiaud you branch looks fine

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-03-01 18:30:53 UTC  
> Url: https://github.com/boostorg/test/issues/196#issuecomment-468763920  

In master, closing.
