# #251 - Context message are always printed if both HRF and Junit loggers are enabled [Closed]

> Username: Fabcien  
> Created at: 2020-02-07 08:25:55 UTC  
> Updated at: 2020-04-21 14:23:39 UTC  
> Closed at: 2020-04-06 12:21:47 UTC  
> Url: https://github.com/boostorg/test/issues/251  

Tested with Boost 1.72, 1.67 and 1.65.  
  
Considering the minimal example:  
```  
#define BOOST_TEST_MODULE Boost log issue  
  
#include <boost/test/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(context_log) {  
        BOOST_TEST_CONTEXT("Should not be displayed") {  
                BOOST_CHECK(true);  
        }  
}  
```  
Built with:  
```  
g++ main.cpp -o boost_demo -DBOOST_TEST_DYN_LINK -lboost_unit_test_framework  
```  
  
I would expect the context message to appear on stdout only when the log level `success` (or above) is set on HRF. As expected:  
  
```  
$ ./boost_demo --logger=HRF  
Running 1 test case...  
  
*** No errors detected  
  
$ ./boost_demo --logger=HRF,all  
Running 1 test case...  
Entering test module "Boost log issue"  
main.cpp(5): Entering test case "context_log"  
main.cpp(7): info: check true has passed  
Assertion occurred in a following context:  
    Should not be displayed  
main.cpp(5): Leaving test case "context_log"; testing time: 526us  
Leaving test module "Boost log issue"; testing time: 630us  
  
*** No errors detected  
```  
  
The issue occurs when I enable the Junit logger as well. Changing the log level of the HRF logger have no impact:  
```  
$ ./boost_demo --logger=HRF:JUNIT  
Running 1 test case...  
  
Assertion occurred in a following context:  
    Should not be displayed  
*** No errors detected  
  
$ ./boost_demo --logger=HRF,nothing:JUNIT  
  
Assertion occurred in a following context:  
    Should not be displayed  
*** No errors detected  
  
$ ./boost_demo --logger=HRF,nothing:JUNIT,nothing  
  
Assertion occurred in a following context:  
    Should not be displayed  
*** No errors detected  
```  
  
This can also happen with the XML logger to some extent, as it requires the XML log level to be set to `success` or above:  
```  
$ ./boost_demo --logger=HRF:XML,message,out.xml  
Running 1 test case...  
  
*** No errors detected  
  
$ ./boost_demo --logger=HRF:XML,success,out.xml  
Running 1 test case...  
  
Assertion occurred in a following context:  
    Should not be displayed  
*** No errors detected  
  
$ ./boost_demo --logger=HRF,nothing:XML,success,out.xml  
  
Assertion occurred in a following context:  
    Should not be displayed  
*** No errors detected  
```  
  
This is causing undesirable and confusing output to our CI log.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-05 22:08:39 UTC  
> Url: https://github.com/boostorg/test/issues/251#issuecomment-609492575  

The branch `topic/GH-251-context-messages-always-printed` contains a fix. It would be good if you can quickly give a try. Thanks!

---

## Comment 2

> Username: Fabcien  
> Created at: 2020-04-06 08:29:39 UTC  
> Url: https://github.com/boostorg/test/issues/251#issuecomment-609651796  

I re-ran the all the tests from my previous post with the branch `topic/GH-251-context-messages-always-printed` and I couldn't reproduce the issue anymore.  
  
The fix is working, thank you !

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-04-06 08:34:33 UTC  
> Url: https://github.com/boostorg/test/issues/251#issuecomment-609654105  

Thank you for the quick feedback! (I'll close the ticket when the fix reaches `master`)

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-04-06 12:22:46 UTC  
> Url: https://github.com/boostorg/test/issues/251#issuecomment-609761167  

Fixed in master
