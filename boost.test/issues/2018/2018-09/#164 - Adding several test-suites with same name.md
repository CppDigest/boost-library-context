# #164 - Adding several test-suites with same name [Closed]

> Username: raffienficiaud  
> Created at: 2018-09-17 21:42:50 UTC  
> Updated at: 2018-09-29 19:39:59 UTC  
> Closed at: 2018-09-29 19:39:59 UTC  
> Url: https://github.com/boostorg/test/issues/164  

See https://svn.boost.org/trac10/ticket/12597

---

## Comment 1

> Username: gsauthof  
> Created at: 2018-09-22 08:57:18 UTC  
> Url: https://github.com/boostorg/test/issues/164#issuecomment-423728744  

You wrote:  
  
> I am not able to reproduce this problem of defining multiple test suites with same name with `BOOST_AUTO_TEST_SUITE`  
  
What about defining test suites with the same name in different translation units with `BOOST_TEST_SUITE`  - is this supported/was this ever supported?  
  
Apparently, this works for me with boost 1.64 but fails with recent boost versions:  
  
    Test setup error: boost::unit_test::framework::setup_error:  
        test unit with name 'somesuite' registered multiple times

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2018-09-22 09:08:43 UTC  
> Url: https://github.com/boostorg/test/issues/164#issuecomment-423729340  

Creating duplicate test suites with `BOOST_TEST_SUITE` was tolerated, but it was in fact creating 2 different test suites with the same name. This is not allowed any more.   
  
*However*, registering multiple times a test suite with `BOOST_AUTO_TEST_SUITE` with the same was always supported. Under the hood, this registration retrieves a suite with the same name - if any - or create a new one.  
  
This logic is here: https://github.com/boostorg/test/blob/4df5b095eaa967f5e2687978bd3d9467b758f28e/include/boost/test/impl/test_tree.ipp#L468.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-29 19:39:56 UTC  
> Url: https://github.com/boostorg/test/issues/164#issuecomment-425670646  

Closing, see rev https://github.com/gsauthof/libgrammar/commit/fe653ae3cfd10e4ec45436551d67c413fe561fc9
