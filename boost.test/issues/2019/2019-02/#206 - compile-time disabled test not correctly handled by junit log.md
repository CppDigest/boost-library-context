# #206 - compile-time disabled test not correctly handled by junit log [Closed]

> Username: romp13  
> Created at: 2019-02-14 21:44:18 UTC  
> Updated at: 2020-01-07 06:44:29 UTC  
> Closed at: 2019-12-02 22:53:30 UTC  
> Url: https://github.com/boostorg/test/issues/206  

Test cases can be disabled at compile-time or at runtime as explained in the doc here: https://www.boost.org/doc/libs/1_66_0/libs/test/doc/html/boost_test/tests_organization/enabling.html  
  
In general, compile-time disabled tests do not appear in the log, and runtime disabled tests do. At least that is the case for HRF and XML format.  
The JUnit log formatter does not follow this. As a result compile-time disabled tests appear in the JUnit report and in addition the "skipped" attribute of the testsuite is wrong and do not account for these.  
  
  
Here is a sample code to reproduce the problem:  
```cpp  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MODULE MyTest  
#include <boost/test/unit_test.hpp>  
namespace utf = boost::unit_test;  
namespace tt = boost::test_tools;  
  
struct skip_with_message  
{  
  std::string message;  
  skip_with_message(std::string m)  
    : message(m) {}  
  
  tt::assertion_result operator()(utf::test_unit_id)  
  {  
    tt::assertion_result ans(false);  
    ans.message() << "test is skipped because " << message;  
    return ans;  
  }  
};  
  
BOOST_AUTO_TEST_CASE( test1 )  
{  
    BOOST_CHECK( 2 + 2 == 4 );  
}  
  
BOOST_AUTO_TEST_CASE( test2, * utf::precondition(skip_with_message("bug")) )  
{  
    BOOST_CHECK_EQUAL(0, 1);  
}  
  
BOOST_AUTO_TEST_CASE( test3, * utf::disabled())  
{  
    BOOST_CHECK_EQUAL(0, 1);  
}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-11-03 12:09:00 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-549130245  

Hi,  
  
This is by design that all test cases, even skipped ones, appear in the final JUNIT. The tools I am using on top of boost.test for instance show me the number of skipped tests, and this is an important piece of information that needs to be reported to developers. This is also in line with other unit testing frameworks I am using in eg. Python, Java, Typescript, etc.  
  
What I would rather do is to show skipped tests in HRF and XML, and improve the logs of the JUNIT to indicate that the skipped test has been disabled at compile-time. Would you agree with this resolution?  
  
PS: please avoid things like `"it sucks !!"`, it does not help the discussion.

---

## Comment 2

> Username: romp13  
> Created at: 2019-11-03 19:33:14 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-549170460  

Hi,  
  
Thanks for looking into this. It's been a while and I don't fully recall all the details.  
I think your proposed solution is good as it fixes the consistency problem between JUnit vs HRF/XML reports. However, the second issue (and the reason why it was a problem for me at the time) is that the 'skipped' attribute of the testsuite did not match the number of skipped tests. From what I have seen some tools (like mine) was relying on this attribute, and other tools were simply counting the number of XML 'test' tag marked as skipped. Does that make sense?  
  
P.S. sorry for the "it sucks" left in the code sample. Probably was having a though debugging session. This was meant at my computer not at boost.test.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-11-03 22:45:09 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-549187114  

Thanks for the feedback (and the edit!). I understand now better the issue and may have a fix for this on branch `topic/GH-206-compile-time-disabled-junit`. It needs some more serious testing though.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-11-06 07:53:03 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-550189606  

Still some corner cases to test, but I believe the issue is fixed.

---

## Comment 5

> Username: romp13  
> Created at: 2019-11-07 20:24:08 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-551248257  

Thanks a lot for the fix. I had a quick look at the code and it looks promising to me. Unfortunately I don't have the time to test it in detail, especially because I don't work at the company where we ran into this issue anymore. But I forwarded the thread to one of my former colleagues, maybe he'll have time to verify it.

---

## Comment 6

> Username: mistafunk  
> Created at: 2019-11-08 08:07:15 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-551427655  

Thanks for the fix, as well! We have multiple test dashboards internally where we can see the discrepancy (testsuite attribute values vs number of skipped tags). We'll be able to verify this fix once there is a rc/beta build of boost 1.72. (patching etc is currently out of scope unfortunately).

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2019-11-11 20:18:23 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-552598199  

There is enough coverage for now. There might be additional issues because of the test tree traversal (breath first, not exploring disabled test units). In case you hit such an issue, please open a new ticket.

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2019-12-02 22:53:30 UTC  
> Url: https://github.com/boostorg/test/issues/206#issuecomment-560815429  

In master, closing.
