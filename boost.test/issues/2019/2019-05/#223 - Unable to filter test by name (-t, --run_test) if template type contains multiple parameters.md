# #223 - Unable to filter test by name (-t, --run_test) if template type contains multiple parameters [Closed]

> Username: TorstenRobitzki  
> Created at: 2019-05-10 11:11:52 UTC  
> Updated at: 2020-01-07 06:44:30 UTC  
> Closed at: 2019-11-03 10:13:53 UTC  
> Url: https://github.com/boostorg/test/issues/223  

Hello,  
I'm used to using the `-t` option to separate a failing test during TDD and to inspect the cause be using printf() debugging. Usually, I just copy the test name just from the error message. So, if I have an error message like this:   
  
    error: in "filter_me<test_parameters<100, 100>>": check sizeof( int ) == sizeof( Param ) has failed [4 != 1]  
  
I'm used to filter the test with: `-t "filter_me<test_parameters<100, 100>>"`. That works fine as long as there is no comma in the test name, as above.  
  
Following example:  
  
    #define BOOST_TEST_MODULE "reproducer"  
    #include <boost/test/included/unit_test.hpp>  
  
    #include <tuple>  
  
    template < int, int >  
    struct test_parameters {};  
  
    using test_cases = std::tuple<  
        test_parameters< 100, 100 >  
    >;  
  
    BOOST_AUTO_TEST_CASE_TEMPLATE( filter_me, Param, test_cases )  
    {  
        BOOST_CHECK_EQUAL( sizeof( int ), sizeof( Param ) );  
    }  
  
Will generate the error message above and will not allow you to filter the test by the name printed. However, the test can be filter with a wildcard behind the first comma: `-t "filter_me<test_parameters<100,*"`.  
  
I would expect that I can use the reported name of a failing test to filter for exactly that failing test.  
  
Edit: Observed in Boost 1.68 with Apple LLVM version 10.0.0

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-05-13 17:52:50 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-491922115  

Thanks for the report. Right now, two possible solutions that come to my mind:  
  
1. Either replace every symbols (`<`, `>`, `,`...) etc by `_`  
1. Or properly handle the name of the test `test_parameters< 100, 100 >` in the filtering  
  
As a user, I find `test_parameters< 100, 100 >` nicer, but it also gives the sense that the framework is able to parse the syntax properly (means that if I have `test_parameters<100,100.      >` it should also work).  
Since 1. is much easier than 2. and 2. can also be misleading, and also considering the fact that we already do that for user provided names, I'll go for 1.   
  
What do you think?

---

## Comment 2

> Username: TorstenRobitzki  
> Created at: 2019-05-13 18:01:22 UTC  
> Updated at: 2019-05-13 18:01:48 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-491925112  

Hi, I would just expect the framework to take the exact same name, that it prints. My expectation would be, that when the framework says test `"filter_me<test_parameters<100, 100>>"` discovered a bug, that I can execute exactly that test by exactly that name (`"filter_me<test_parameters<100, 100>>"`). (Which by the way, works most of the time).

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-05-13 18:24:02 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-491933059  

We both agree to qualify the current behaviour that you described as buggy, and that was not my question.   
  
Thanks for the report, I'll go for 1.

---

## Comment 4

> Username: TorstenRobitzki  
> Created at: 2019-05-14 05:38:22 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-492085898  

Hi,  
sorry for not understanding your question! :-) I'm ok with the names of the symbols as they are now, but I would be ok if they would change.  
  
Thank you for looking into this!

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-10-30 20:34:31 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-548100657  

I implemented a fix on the branch `topic/GH-223-cannot-filter-template-test-cases`. Would it be possible for you to try?

---

## Comment 6

> Username: TorstenRobitzki  
> Created at: 2019-11-02 12:18:52 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-549038704  

Works like a charm. Thank you very much!

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2019-11-02 16:53:46 UTC  
> Url: https://github.com/boostorg/test/issues/223#issuecomment-549061503  

Thanks for the quick feedback! (I'll close the issue when this gets merged to master)
