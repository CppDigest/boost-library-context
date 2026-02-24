# #97 - is_member_func_test fails for GCC 4.8.1 [Closed]

> Username: joaquintides  
> Created at: 2018-11-20 08:39:31 UTC  
> Updated at: 2018-12-05 14:32:07 UTC  
> Closed at: 2018-11-26 17:41:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/97  

`is_member_func_test` fails for GCC 4.8.1 as shown in  
  
https://www.boost.org/development/tests/master/developer/output/igaztanaga-master-gcc-4-8c++11-boost-bin-v2-libs-type_traits-test-is_member_func_test-test-gcc-4-8c+-dbg-dbg-symbl-off-vsblt-hdn.html  
  
The problem here is that `is_member_function_pointer.hpp` dispatches to a C++11 implementation or a C++03 one depending on whether `BOOST_TT_HAS_ASCCURATE_IS_FUNCTION` is defined or not:  
  
https://github.com/boostorg/type_traits/blob/17af798061b84661de72e2b65ecaf17ae0e43cfa/include/boost/type_traits/detail/config.hpp#L84-L87  
  
And `BOOST_GCC` is 40801 for the offending compiler. I guess the fix is as simple as changing `BOOST_WORKAROUND(BOOST_GCC, <= 40800)` to `BOOST_WORKAROUND(BOOST_GCC, < 40900)`.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-11-26 16:32:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/97#issuecomment-441705269  

Attention, this is on the `master` branch and 1.69 release will be closed in less than two weeks.  
  
Also, there is a strange part in the fail: other GCC 4.8 runner (teeks99-02-mg4.8-Docker-64on64 gcc-4.8~c++11) does not fail; Travis CI has 4.8 job and it did not fail too. It looks like there is some thing in MinGW GCC.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-11-26 17:43:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/97#issuecomment-441730559  

Yeah, it's strange that it passes CI.  The intent was to exclude gcc-4.8 from the C++11 branch so the fix is the right one in any case.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-11-26 17:45:37 UTC  
> Url: https://github.com/boostorg/type_traits/issues/97#issuecomment-441731285  

The travis job is gcc-4.8.5 - so it's possible the fix is no longer required at that patch level I guess.

---

## Comment 4

> Username: joaquintides  
> Created at: 2018-12-05 14:32:07 UTC  
> Url: https://github.com/boostorg/type_traits/issues/97#issuecomment-444504950  

I'm afraid the commit has not been merged to master, and Boost is shipping today.
