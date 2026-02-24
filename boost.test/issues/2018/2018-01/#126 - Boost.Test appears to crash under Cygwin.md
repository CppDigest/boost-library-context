# #126 - Boost.Test appears to crash under Cygwin [Closed]

> Username: pdimov  
> Created at: 2018-01-17 23:34:56 UTC  
> Updated at: 2018-01-19 08:16:59 UTC  
> Closed at: 2018-01-19 08:16:59 UTC  
> Url: https://github.com/boostorg/test/issues/126  

The tests for any Boost library that uses Boost.Test seem to crash for me under Cygwin with:  
  
```  
Starting program: /cygdrive/c/boost-git/develop/bin.v2/libs/range/test/std_conta  
iner.test/gcc-6.4.0/debug/link-static/target-os-cygwin/threading-multi/std_conta  
iner.exe  
[New Thread 56732.0xb798]  
[New Thread 56732.0x8d3c]  
[New Thread 56732.0xb228]  
[New Thread 56732.0xd984]  
  
Program received signal SIGSEGV, Segmentation fault.  
0x004238ab in boost::unit_test::framework::get (id=2147824840,  
    t=boost::unit_test::TUT_CASE) at ../boost/test/impl/framework.ipp:1490  
1490        BOOST_TEST_I_ASSRT( (res->p_type & t) != 0, internal_error( "Invalid  
 test unit type" ) );  
(gdb) bt  
#0  0x004238ab in boost::unit_test::framework::get (id=2147824840,  
    t=boost::unit_test::TUT_CASE) at ../boost/test/impl/framework.ipp:1490  
#1  0x0044611d in boost::unit_test::framework::get<boost::unit_test::test_case>  
(id=2147824840) at ../boost/test/framework.hpp:232  
#2  0x00405dbd in boost::unit_test::traverse_test_tree (id=2147824840, V=...,  
    ignore_status=true) at ../boost/test/impl/test_tree.ipp:363  
#3  0x00423041 in boost::unit_test::framework::finalize_setup_phase (  
    master_tu_id=2147824840) at ../boost/test/impl/framework.ipp:1163  
#4  0x0040b067 in boost::unit_test::unit_test_main (init_func=  
    0x401ef6 <init_unit_test_suite(int, char**)>, argc=1, argv=0x28cc0c)  
    at ../boost/test/impl/unit_test_main.ipp:200  
#5  0x0040b4a0 in main (argc=1, argv=0x28cc0c)  
    at ../boost/test/impl/unit_test_main.ipp:286  
```  
  
This example is for Range's `std_container.cpp`.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-01-18 06:12:24 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358548199  

This part of the code has not changed since some time. Is this new to you, or something changed in the environment (cygwin version)?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-18 12:40:41 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358634947  

No, it's not new. I just assumed that the crash is due to a bug in Cygwin exception handling or something like that, before I finally identified Boost.Test as the cause when I noticed that only tests using Boost.Test crash.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-01-18 12:56:03 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358638614  

I'll have a look then soon, and keep you updated.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-01-18 23:26:22 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358816302  

Based on the stack trace (and given the fact that I had no time to check on Cygwin), I am proposing a blind fix in the branch [GH1-cygwin-crash](https://github.com/boostorg/test/tree/topic/GH1-cygwin-crash) .   
  
It would be nice if you can give a try to this branch and keep me posted.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-01-19 01:41:42 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358840549  

This appears to have fixed things; thanks! I ran the whole Range test suite, without any crashes.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-01-19 06:14:07 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358876653  

Perfect, I merge to develop ASAP.

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2018-01-19 08:16:57 UTC  
> Url: https://github.com/boostorg/test/issues/126#issuecomment-358896396  

Ticket https://svn.boost.org/trac10/ticket/13407, branch ``topic/13407-cygwin-crash`` under CI tests. Closing this one, follow up on Trac
