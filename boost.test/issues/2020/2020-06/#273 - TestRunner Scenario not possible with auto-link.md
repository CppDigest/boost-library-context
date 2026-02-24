# #273 - TestRunner Scenario not possible with auto-link. [1.73] [Open]

> Username: Faaux  
> Created at: 2020-06-02 10:51:24 UTC  
> Updated at: 2020-06-09 11:37:55 UTC  
> Url: https://github.com/boostorg/test/issues/273  

Hey everyone,  
I am using a test scenario where we build DLLs containing the tests with a custom init_unit_test function. For this we want to dynamically link to Boost.Test and thus are defining BOOST_TEST_DYN_LINK as can be seen in the [documentation](https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/adv_scenarios/external_test_runner.html).  
  
The DLL does not need a main so BOOST_TEST_MAIN is not being defined. We are using the console_test_runner which is also dynamically linked to run our tests.  
  
The issue:  
  
As per this commit: https://github.com/boostorg/test/commit/961646222e4cf0eb475a86667a71ce89e4994d5c BOOST_TEST_MAIN has to be defined for auto linking to work. This is not the case when using a test_runner scenario.  
  
Defining BOOST_TEST_MAIN and BOOST_TEST_NO_MAIN as a workaround does not work because now init_unit_test is defined twice.  
  
Am I missing a piece of documentation for this to work?  
  
BOOST 1.73 on MSVC v142  
  
Best,  
Faaux

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-06-02 17:33:55 UTC  
> Url: https://github.com/boostorg/test/issues/273#issuecomment-637698914  

Hi!  
  
It would be helpful if you post a piece of code (together with the defines) that help me understand with /precision/ what your issue is. From what you are saying, I have the feeling that either `BOOST_TEST_MAIN` or `BOOST_TEST_NO_MAIN` should be defined in order to activate the section of `#  define BOOST_LIB_NAME boost_unit_test_framework` (right now only `BOOST_TEST_MAIN` is checked).  
  
The workaround that you have today is to link explicitly to boost.test: IMO it is the easiest.  
  
In all cases, let me know if my understanding of the issue is correct. You may check quickly by replacing [this line](https://github.com/boostorg/test/blob/develop/include/boost/test/unit_test.hpp#L29) with  
  
```  
(defined(BOOST_TEST_MAIN) || defined(BOOST_TEST_NO_MAIN))  
```

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-06-02 17:50:06 UTC  
> Url: https://github.com/boostorg/test/issues/273#issuecomment-637706855  

Also I would like to have your opinion about the test runner feature: what are the advantages in using such a setup compared to the more standard dynamic linking? binary size? compilation speed? tests management?   
  
I am asking because I never used this feature, and I would be interested in what makes it valuable. I may improve the documentation as well.

---

## Comment 3

> Username: Faaux  
> Created at: 2020-06-09 10:38:41 UTC  
> Url: https://github.com/boostorg/test/issues/273#issuecomment-641202841  

The benefits are test management outside of the project itself. The line you recommended solved the problem for us. Before it was not possible to dynamic link without defining `BOOST_TEST_MAIN`.  
  
Thank you for your time and quick anwser. For me this can be closed!

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-06-09 11:37:54 UTC  
> Url: https://github.com/boostorg/test/issues/273#issuecomment-641234140  

> The line you recommended solved the problem for us  
  
Thanks for the feedback! I'll close the issue when the change lands to master
