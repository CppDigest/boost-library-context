# #163 - Significant start slowdown on MSVC x64/Debug after upgrade to v1.68.0 [Closed]

> Username: k15tfu  
> Created at: 2018-09-17 12:44:56 UTC  
> Updated at: 2018-09-24 18:01:56 UTC  
> Closed at: 2018-09-24 18:01:55 UTC  
> Url: https://github.com/boostorg/test/issues/163  

Hi!  
  
I'm experiencing a very bad problem:  I have a one boost test executable which contains over 50.000 test cases (data test cases mostly), and after upgrade to the latest Boost.Test the startup time has significantly increased from <3sec up to ~7min.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-17 18:52:36 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422129085  

Can you post the test module (at least the init part)? I'll have a look

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-09-17 19:40:50 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422143909  

main.cpp  
```  
// BOOST_TEST_MODULE should:  
// 1. be defined before any inclusion directive to Unit Test Framework headers  
// 2. be defined exactly for one compilation unit of your test module  
#define BOOST_TEST_MODULE Common  
  
// Include header for `BOOST_TEST_MODULE`.  
#include <boost/test/included/unit_test.hpp>  
```  
  
In other files we just include `boost/test/unit_test.hpp` and then define test cases by `BOOST_AUTO_TEST_CASE`. After that the main.o is linked together with foo_test.o (& Co.) into the mentioned .exe file.

---

## Comment 3

> Username: ww898  
> Created at: 2018-09-17 19:43:39 UTC  
> Updated at: 2018-09-17 19:51:11 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422144807  

Hi there,  
I have the same issue in my project. My investigation shows that method `traverse_test_tree()` works very slow https://github.com/boostorg/test/blob/develop/include/boost/test/impl/framework.ipp#L1199. Most time it spends in `test_suite::add()` on `BOOST_TEST_SETUP_ASSERT()` https://github.com/boostorg/test/blob/develop/include/boost/test/impl/test_tree.ipp#L248. The startup time increased in 2.5 times to 20min.  
  
Another issue is test filters apply after collecting all tests. It means that command line option `--run_test` can't decrease the initialization tests time.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-09-17 20:26:15 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422158493  

@ww898 I see now why it is so. Out of curiosity, how much time does it take in release?

---

## Comment 5

> Username: ww898  
> Created at: 2018-09-17 22:19:25 UTC  
> Updated at: 2018-09-17 22:20:58 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422190537  

@raffienficiaud Well, I measured the execution time for one test which was chosen by the command line option `--run_test`:  
  
| MSVC v14.13.26128 | boost v1.66.0 | boost v1.68.0 | in times slower |  
|-|-:|-:|-:|  
| Debug | 55s,048ms | 20m:50s,339ms | 22.71 |  
| Release | 34s,174ms | 1m:43s,055ms | 3.01 |

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-09-17 22:24:54 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422191806  

Thanks! That is quite a lot indeed!  
I delayed the evaluation of the duplicate test cases, which seems to be the hotspot. It is still under tests, but you may want to check the branch `topic/GH-163-slowdown-at-startup` .

---

## Comment 7

> Username: k15tfu  
> Created at: 2018-09-18 18:04:11 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422491389  

Thank you guys for help!

---

## Comment 8

> Username: ww898  
> Created at: 2018-09-18 20:13:28 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422535646  

@raffienficiaud Thank you very much for `topic/GH-163-slowdown-at-startup` branch:  
  
| MSVC v14.13.26128 | boost v1.66.0 | boost v1.68.0 | boost v1.68.0 + branch |  
|-|-:|-:|-:|  
| Debug | 55s,048ms | 20m:50s,339ms | 53s,500ms |  
| Release | 34s,174ms | 1m:43s,055ms | 34s,909ms  |

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2018-09-18 20:15:21 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-422536385  

Thanks for testing! I'll merge once I fix this visibility issues

---

## Comment 10

> Username: raffienficiaud  
> Created at: 2018-09-24 18:01:55 UTC  
> Url: https://github.com/boostorg/test/issues/163#issuecomment-424068521  

Merged to develop
