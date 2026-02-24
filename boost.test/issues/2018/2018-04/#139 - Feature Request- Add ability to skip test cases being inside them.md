# #139 - Feature Request: Add ability to skip test cases being inside them [Open]

> Username: k15tfu  
> Created at: 2018-04-04 18:21:05 UTC  
> Updated at: 2021-01-25 03:14:34 UTC  
> Url: https://github.com/boostorg/test/issues/139  

Hi!  
  
I want to use something similar to skip() method in python unittest: https://docs.python.org/2/library/unittest.html#skipping-tests-and-expected-failures .  
The goal is to check at runtime whether this test case should be run (or should be ignored in the results) or not. For example, if some condition is true we can do BOOST_SKIP("There is no enough free disk space on your computer") and this test case will not be counted as "failed".  Instead, probably there can be another column "skipped" which can collect statistics about skipped test cases.  
  
Plan B (the simplest workaround): Is to allow to add expected_failure decorator to a test, being inside the test. For example, in this case we can do something like:  
```  
if (...) {  
  current_test_case()->add_decorator(*expected_failures(1));  
  BOOST_FAIL("There is no enough free disk space on your computer");  
}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-04-05 05:12:11 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-378822149  

I believe you are experiencing this bug: https://svn.boost.org/trac10/ticket/12095 . I have a fix for this bug but I was not able to test it thoroughly, so it did not go to 1.67. Maybe you can give a try? It is the branch `topic/12095-disabling-test-with-precondition-leads-to-error`.

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-04-16 12:37:57 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-381584613  

Recently I reported a bug that disabled() doesn't work for BOOST_DATA_TEST_CASE(s). Something says me precondition() will not work also =)  Yes, probably I can give it a try for auto test cases later.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-01 19:16:19 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-417881121  

The fix is now in 1.68. Would you mind give another try to the boost.test preconditions?

---

## Comment 4

> Username: k15tfu  
> Created at: 2018-10-02 18:29:36 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-426382055  

@raffienficiaud preconditon() works , I was wrong. But I have some usability problem: I cannot skip some sub-cases of `BOOST_DATA_TEST_CASE`, it skips the whole data test case.  
  
My case is: I have a custom implementation of ftruncate() covered with data test cases. One of those `BOOST_DATA_TEST_CASE` checks the maximum possible offset for ftruncate(), like that it works for data::make({4GB, 10GB, 20GB, 50GB and so on}). The problem is that these test cases are working on build agents which have 14GB of free space (guaranteed), but sometimes it's bigger.  My goal is to skip `{20GB, 50GB, ...}` cases if the agents don't have that much free disk space.  
  
With current implementation if the precondition fails the whole BOOST_DATA_TEST_CASE will be skipped =(.  Any ideas? (except splitting test sets to those with normal and abnormal requirements)

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-02-14 20:21:02 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-463779339  

With the fixes 52c7da62d83f5e33fba25dd8e83ae27e260ece7a / #138 and 0b4eeb22c6266875c1aba6f8d499ce5367eb8a42 / #203 , I would be happy if you can try `preconditon()` again. This decorator will be applied to each of the test in a `BOOST_DATA_TEST_CASE`. It is in `next-internal` now.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-03-02 09:36:02 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-468904399  

Changes are in master now, it would be good if you can give a try.

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2019-03-13 20:33:24 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-472593992  

@k15tfu Kind reminder.

---

## Comment 8

> Username: k15tfu  
> Created at: 2019-03-14 12:58:58 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-472841923  

@raffienficiaud Hi again =) Sorry for long delay, I have no stable environment to test your changes at any time =(  
  
As I said earlier today, decorators for data test cases are now fixed, but I didn't realized how to use them in this case. The current problem is that precondition doesn't get data test case arguments (like `digit`).  
Is there a way to get test case arguments having `boost::unit_test::test_unit_id`?  
```  
 29 BOOST_TEST_DECORATOR(*boost::unit_test::decorator::precondition(  
 30         [] (boost::unit_test::test_unit_id) {  
 31                 return false;  
 32         }))  
 33 BOOST_DATA_TEST_CASE(check_even_div_2, boost::unit_test::data::make({0, 1, 3, 4}), digit) {  
 34     BOOST_CHECK(digit % 2 == 0);  
 35 }  
```

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2019-03-15 07:23:54 UTC  
> Updated at: 2019-03-15 07:24:18 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-473184608  

Thanks for taking the time to test!  
  
It is not possible to get the arguments of the data test case (`digit` here) inside the precondition function. The function passed to the the `precondition` decorator has a fixed signature.   
  
The rationale behind this is the following: skipping test should not have to do with the arguments passed to the tests. Skipping is rather something that happens because of "external reasons" to the test, so to speak.   
  
For achieving what you want, to me the best would consist in having the right dataset generated in the first place and that suits your needs. You can have a look at the current 1.70 beta documentation [here](https://www.boost.org/doc/libs/1_70_0_beta1/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/datasets.html) for an example of custom dataset, the implementation is quite easy. Maybe some existing ones like [range](https://www.boost.org/doc/libs/1_70_0_beta1/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/generators.html#boost_test.tests_organization.test_cases.test_case_generation.generators.ranges) cover already your needs, or maybe it is achievable by simple dataset composition with `+`.  
  
A more hackish way would be to return from the test function when your conditions are not met, but this is not really a test that should be executed, so I would not invest much on that.  
  
Hope this helps, let me know what you think.

---

## Comment 10

> Username: k15tfu  
> Created at: 2019-03-29 07:29:53 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-477897614  

Okay, I got your reasoning. I just wanted to suggest it because some other frameworks (not for C++) are able to skip tests being inside them, and it was pretty convenient for me =).  
  
For now, yes, I just do return from tests if the condition doesn't meet, as you suggested :) and i think i leave it as is.  
  
thank you again :)

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2019-03-29 08:10:29 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-477907677  

If you point me to the other frameworks that you are mentioning, maybe it will give me some ideas to implement in boost.test.   
  
Thanks again & cheers,  
Raffi

---

## Comment 12

> Username: k15tfu  
> Created at: 2019-03-29 12:19:51 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-477977883  

Specifically, I was talking about golang testing: https://splice.com/blog/lesser-known-features-go-test/ (in Skipping tests)

---

## Comment 13

> Username: csm10495  
> Created at: 2019-12-17 06:46:44 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-566406253  

Found this thread. The ability to BOOST_SKIP would help bring boost test in line with some other frameworks like:  
  
For Python:   
pytest:  
```  
pytest.skip(<str: reason>)  
```  
  
unittest:  
```  
unittest.skip(<str: reason>)  
```  
  
For C++  
  
GTest:  
```  
GTEST_SKIP()  
```  
  
For me I have a couple tests that mid-way through I realize aren't valid, so right now I just return, though an official way to skip at that moment to get it marked as skipped in things like JUnit XML would be nice.

---

## Comment 14

> Username: raffienficiaud  
> Created at: 2019-12-17 12:35:27 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-566523404  

Ok, you got me, reopening!  
  
How should we handle the fact when some tests have already passed and then we ask for skipping? I am just curious to know how Gtest or pytest handle  this, also in their respective logs. What I mean is this  
  
```  
BOOST_DATA_TEST_CASE(check_even_div_2, boost::unit_test::data::make({0, 1, 3, 4}), digit) {  
    BOOST_TEST(digit % 2 == 0);  
    if(digit % 2 == 1)  
         BOOST_TEST_SKIP();  
}  
```  
  
Currently a test in Boost.Test is either running or not, the meaning of skip in the middle is not so clear to me. The meaning of `skip` at the beginning of the test is ok, although the fixture may contain some tests as well ...

---

## Comment 15

> Username: csm10495  
> Created at: 2019-12-17 18:52:32 UTC  
> Updated at: 2019-12-17 18:52:49 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-566698488  

Here is a simple example for pytest:  
  
```  
import pytest  
  
def test_a_pass():  
    pass  
  
def test_a_fail():  
    assert 0, "ouch"  
  
def test_a_skip():  
    pytest.skip("skipping!")  
```  
  
Then in the test result we see:  
  
```  
1 failed, 1 passed, 1 skipped  
```  
  
The junitxml looks like this:  
  
```  
<?xml version="1.0" encoding="utf-8"?><testsuites><testsuite errors="0" failures="1" hostname="system" name="pytest" skipped="1" tests="3" time="0.071" timestamp="2019-12-17T10:51:42.286052"><testcase classname="test" file="test.py" line="3" name="test_a_pass" time="0.001"></testcase><testcase classname="test" file="test.py" line="6" name="test_a_fail" time="0.000"><failure message="AssertionError: ouch  
assert 0">def test_a_fail():  
&gt;       assert 0, &quot;ouch&quot;  
E       AssertionError: ouch  
E       assert 0  
  
test.py:8: AssertionError</failure></testcase><testcase classname="test" file="test.py" line="9" name="test_a_skip" time="0.000"><skipped message="skipping!" type="pytest.skip">Desktop\tmp\test.py:11: skipping!</skipped></testcase></testsuite></testsuites>  
```

---

## Comment 16

> Username: csm10495  
> Created at: 2019-12-17 19:01:28 UTC  
> Updated at: 2019-12-17 19:04:17 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-566701998  

Here is a bit more complicated example for pytest (using a fixture):  
```  
import pytest  
  
@pytest.fixture(scope='function')  
def setupAndTeardown(request):  
    # setup goes here  
    if request.node.name == 'test_a_fail_in_setup':  
        assert 0, "This test should fail at this point"  
  
    if request.node.name == 'test_a_skip_in_setup':  
        pytest.skip("skipping")  
  
    try:  
        yield  
    finally:  
        # teardown goes here (if we want it to always run)  
        if request.node.name == 'test_a_fail_in_teardown':  
            assert 0, "This test should fail at this point"  
  
        if request.node.name == 'test_a_skip_in_teardown':  
            pytest.skip("skipping")  
  
  
def test_a_pass(setupAndTeardown):  
    pass  
  
def test_a_fail_in_setup(setupAndTeardown):  
    pass  
  
def test_a_skip_in_setup(setupAndTeardown):  
    pass  
  
def test_a_fail_in_teardown(setupAndTeardown):  
    pass  
  
def test_a_skip_in_teardown(setupAndTeardown):  
    pass  
  
def test_a_skip(setupAndTeardown):  
    pytest.skip("skipping!")  
```  
  
Then in the test result we see:  
```  
3 passed, 3 skipped, 2 errors  
```  
  
The junitxml looks like this:  
```  
<?xml version="1.0" encoding="utf-8"?><testsuites><testsuite errors="2" failures="0" hostname="system" name="pytest" skipped="3" tests="8" time="0.093" timestamp="2019-12-17T11:02:53.040864"><testcase classname="test" name="test_a_pass" time="0.000"></testcase><testcase classname="test" name="test_a_fail_in_setup" time="0.002"><error message="test setup failure">request = &lt;SubRequest &apos;setupAndTeardown&apos; for &lt;Function test_a_fail_in_setup&gt;&gt;  
  
    @pytest.fixture(scope=&apos;function&apos;)  
    def setupAndTeardown(request):  
        # setup goes here  
        if request.node.name == &apos;test_a_fail_in_setup&apos;:  
&gt;           assert 0, &quot;This test should fail at this point&quot;  
E           AssertionError: This test should fail at this point  
E           assert 0  
  
test.py:7: AssertionError</error></testcase><testcase classname="test" name="test_a_skip_in_setup" time="0.000"><skipped message="skipping" type="pytest.skip">\tmp\test.py:10: skipping</skipped></testcase><testcase classname="test" name="test_a_fail_in_teardown" time="0.001"><error message="test teardown failure">request = &lt;SubRequest &apos;setupAndTeardown&apos; for &lt;Function test_a_fail_in_teardown&gt;&gt;  
  
    @pytest.fixture(scope=&apos;function&apos;)  
    def setupAndTeardown(request):  
        # setup goes here  
        if request.node.name == &apos;test_a_fail_in_setup&apos;:  
            assert 0, &quot;This test should fail at this point&quot;  
      
        if request.node.name == &apos;test_a_skip_in_setup&apos;:  
            pytest.skip(&quot;skipping&quot;)  
      
        try:  
            yield  
        finally:  
            # teardown goes here (if we want it to always run)  
            if request.node.name == &apos;test_a_fail_in_teardown&apos;:  
&gt;               assert 0, &quot;This test should fail at this point&quot;  
E               AssertionError: This test should fail at this point  
E               assert 0  
  
test.py:17: AssertionError</error></testcase><testcase classname="test" name="test_a_skip_in_teardown" time="0.001"><skipped message="skipping" type="pytest.skip">\tmp\test.py:20: skipping</skipped></testcase><testcase classname="test" name="test_a_skip" time="0.000"><skipped message="skipping!" type="pytest.skip">\tmp\test.py:39: skipping!</skipped></testcase></testsuite></testsuites>  
```  
  
(made an edit to have more examples)

---

## Comment 17

> Username: nigels-com  
> Created at: 2021-01-25 03:14:34 UTC  
> Url: https://github.com/boostorg/test/issues/139#issuecomment-766512141  

Bumped into this same limitation today.  The test neither passed or failed, it just needed to be skipped due to a missing optional dependency, or what-not.  Workaround for me is to (misleadingly) pass the test.
