# #38 Some fixes of boost.test init and link fix [Merged]

> Username: raffienficiaud  
> Created at: 2015-01-11 15:24:32 UTC  
> Updated at: 2015-01-11 17:32:48 UTC  
> Merged at: 2015-01-11 15:58:14 UTC  
> Closed at: 2015-01-11 15:58:14 UTC  
> Url: https://github.com/boostorg/thread/pull/38  

As discussed on the boost.test github portal, please find attached the way to go to fix these issues. Note that I did not solve all files, the error being on boost.thread :)  
  
Best,  
Raffi

---

## Comment 1

> Username: viboes  
> Created_at: 2015-01-11 15:49:20 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69498838  

As I said, this doesn't works on my configuration (See the Boost.Test github)

---

## Comment 2

> Username: viboes  
> Created_at: 2015-01-11 15:56:14 UTC  
> Updated_at: 2015-01-11 15:58:05 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69499077  

I did also this change  
  
```  
    [ run $(sources) ../src/tss_null.cpp ../build//boost_thread/<link>static  
        : : :  
      <library>/boost/test//boost_unit_test_framework  
    : $(sources[1]:B)_lib  
    ]  
```  
  
but this doesn't work. When I take exactly your patch for the Jamfile and test 2309, the test 2309 works again. I will apply the merge.  
  
BTW, I'm wondering if other libraries could have the same issue, and if this changes would work on the release branch.

---

## Comment 3

> Username: viboes  
> Created_at: 2015-01-11 15:59:24 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69499200  

Hrr, the patch was for the master branch :(

---

## Comment 4

> Username: viboes  
> Created_at: 2015-01-11 16:02:02 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69499317  

Next follows the errors I got after the merge on the master branch :(  
  
```  
darwin.compile.c++ ../../../bin.v2/libs/thread/test/test_2741_lib.test/darwin-4.9.1/debug/threading-multi/test_2741.o  
test_2741.cpp: In function ‘boost::unit_test::test_suite* init_unit_test_suite(int, char**)’:  
test_2741.cpp:78:41: erreur: redefinition of ‘boost::unit_test::test_suite* init_unit_test_suite(int, char**)’  
 boost::unit_test_framework::test_suite* init_unit_test_suite(int, char*[])  
                                         ^  
In file included from ../../../boost/test/unit_test.hpp:19:0,  
                 from test_2741.cpp:16:  
../../../boost/test/unit_test_suite.hpp:235:1: note: ‘boost::unit_test::test_suite* init_unit_test_suite(int, char**)’ previously defined here  
 init_unit_test_suite( int, char* [] )   {  
 ^  
../../../boost/test/unit_test_suite.hpp:28:59: erreur: expected primary-expression before ‘(’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                           ^  
test_2741.cpp:82:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_native_handle));  
             ^  
../../../boost/test/unit_test_suite.hpp:28:73: erreur: expected primary-expression before ‘)’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                                         ^  
test_2741.cpp:82:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_native_handle));  
             ^  
../../../boost/test/unit_test_suite.hpp:28:59: erreur: expected primary-expression before ‘(’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                           ^  
test_2741.cpp:83:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_stack_size));  
             ^  
../../../boost/test/unit_test_suite.hpp:28:73: erreur: expected primary-expression before ‘)’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                                         ^  
test_2741.cpp:83:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_stack_size));  
             ^  
../../../boost/test/unit_test_suite.hpp:28:59: erreur: expected primary-expression before ‘(’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                           ^  
test_2741.cpp:84:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_creation_with_attrs));  
             ^  
../../../boost/test/unit_test_suite.hpp:28:73: erreur: expected primary-expression before ‘)’ token  
 boost::unit_test::make_test_case( boost::function<void ()>(test_function), \  
                                                                         ^  
test_2741.cpp:84:13: note: in expansion of macro ‘BOOST_TEST_CASE’  
   test->add(BOOST_TEST_CASE(test_creation_with_attrs));  
             ^  
  
    "/usr/gcc-4.9.1/bin/g++-4.9.1"  -ftemplate-depth-128 -fstack-protector-all -Wno-unused-local-typedefs -Wno-variadic-macros -O0 -fno-inline -Wall -pedantic -g -dynamic -gdwarf-2 -fexceptions -Wno-long-long -fPIC -Wextra -Wno-long-long -Wno-unused-parameter -Wno-variadic-macros -Wunused-function -fpermissive -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_THROW_IF_PRECONDITION_NOT_SATISFIED -DBOOST_THREAD_USE_LIB=1 -DBOOST_TIMER_STATIC_LINK=1  -I"../../.." -c -o "../../../bin.v2/libs/thread/test/test_2741_lib.test/darwin-4.9.1/debug/threading-multi/test_2741.o" "test_2741.cpp"  
```  
  
and much more ....

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2015-01-11 16:29:47 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69500263  

Please read more carefully what I am saying.  
There should not be any `boost::unit_test::test_suite* init_unit_test_suite(int, char**)` anymore in _your_ code. The error is on _your_ side, because you are enforcing the static link of boost.test while you are compiling the dynamic version of boost.thread.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2015-01-11 16:30:35 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69500302  

and also, I did not change all files, I sent you a patch for a quick fix on **some** files, as precised in the PR.

---

## Comment 7

> Username: viboes  
> Created_at: 2015-01-11 16:39:28 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69500623  

The Boost.Thread test worked well before your change. It works well if I rollback your change.  
  
With your change I need to change all the files. I have created a new rule so that I apply the old rule to the files you have not changed :(

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2015-01-11 16:44:08 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69500794  

Yet, it does not mean in any way that the problem is not in boost.thread. WE NEED a new dependency in boost.test, and this is what is done on OUR side. If the chain of dependency breaks in your side, this is your problem.  
Boost.thread does not honour the chain of dependencies properly. There is no doubt in that now.  
  
Thank you to make the changes I proposed. I cannot do more that what i did, which is:  
- debugging the configuration of boost.thread  
- proposing fixes for boost.thread  
- proposing a pull request on some of the files that are breaking ion boost.thread

---

## Comment 9

> Username: viboes  
> Created_at: 2015-01-11 16:52:47 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69501105  

I agree that there is a  problem in  Boost.Thread Jamfile. Just you must be more careful while comiting changes in Boost.Test.  
  
Could you explain why you need to change the sources in Boost.Thread. What was wrong with the old sources?

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2015-01-11 17:20:41 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69502215  

I do not think your way: I do not have to run all boost unit tests for every commit I make, boost is modular. If there is a problem on one client of boost.test, I am happy to help fix those or to accept any PR that fix those. But maybe I'm mistaken (and I do not even know how to run the test for all libraries).

---

## Comment 11

> Username: viboes  
> Created_at: 2015-01-11 17:32:48 UTC  
> Url: https://github.com/boostorg/thread/pull/38#issuecomment-69502720  

No problem. I have already fixed every error.  
IMHO, you should know.

---
