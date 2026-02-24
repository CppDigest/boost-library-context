# #42 - GCC 12 emits `-Wuninitialized` warnings [Closed]

> Username: jngrad  
> Created at: 2022-05-30 14:16:32 UTC  
> Updated at: 2022-05-30 18:18:15 UTC  
> Closed at: 2022-05-30 17:55:53 UTC  
> Url: https://github.com/boostorg/function/issues/42  

The GCC `-Wmaybe-uninitialized` warning reported in #27 was silenced by a diagnostic pragma in #38, however in GCC 12 the warning has become `-Wuninitialized`. Reproducible on Fedora with `gcc-c++-12.0.1-0.16.fc36` and `gcc-c++-12.1.1-1.fc37`. Probably relevant part of the GCC 12 changelog: [Eliminating uninitialized variables](https://gcc.gnu.org/gcc-12/changes.html#uninitialized).  
  
MWE:  
```c++  
#include <boost/test/unit_test.hpp>  
  
struct my_precondition {  
  boost::test_tools::assertion_result operator()(boost::unit_test::test_unit_id) {  
    return false;  
  }  
};  
  
BOOST_TEST_DECORATOR(*boost::unit_test::precondition(my_precondition()))  
BOOST_AUTO_TEST_CASE(my_test) {  
}  
```  
Output ([live example](https://godbolt.org/z/zzeT9nrjd)):  
```  
$ g++ -Wall -O3 -c mwe.cpp  
In file included from /usr/include/boost/function/detail/maybe_include.hpp:22,  
                 from /usr/include/boost/function/function1.hpp:11,  
                 from /usr/include/boost/test/tree/decorator.hpp:30,  
                 from /usr/include/boost/test/tools/fpc_tolerance.hpp:19,  
                 from /usr/include/boost/test/tools/fpc_op.hpp:19,  
                 from /usr/include/boost/test/test_tools.hpp:54,  
                 from /usr/include/boost/test/unit_test.hpp:18,  
                 from mwe.cpp:1:  
In member function ‘void boost::function1<R, T1>::assign_to_own(const boost::function1<R, T1>&) [with R = boost::test_tools::assertion_result; T0 = long unsigned int]’,  
    inlined from ‘boost::function1<R, T1>::function1(const boost::function1<R, T1>&) [with R = boost::test_tools::assertion_result; T0 = long unsigned int]’ at /usr/include/boost/function/function_template.hpp:746:26,  
    inlined from ‘boost::function<R(T0)>::function(const self_type&) [with R = boost::test_tools::assertion_result; T0 = long unsigned int]’ at /usr/include/boost/function/function_template.hpp:1105:76,  
    inlined from ‘boost::unit_test::decorator::precondition::precondition(predicate_t)’ at /usr/include/boost/test/tree/decorator.hpp:280:61,  
    inlined from ‘void __static_initialization_and_destruction_0(int, int)’ at mwe.cpp:9:1,  
    inlined from ‘(static initializers for mwe.cpp)’ at mwe.cpp:11:1:  
/usr/include/boost/function/function_template.hpp:910:22: warning: ‘*(unsigned char (*)[24])((char*)&<unnamed> + offsetof(boost::predicate_t, boost::function<boost::test_tools::assertion_result(long unsigned int)>::<unnamed>.boost::function1<boost::test_tools::assertion_result, long unsigned int>::<unnamed>.boost::function_base::functor))’ is used uninitialized [-Wuninitialized]  
  910 |           std::memcpy(this->functor.data, f.functor.data, sizeof(boost::detail::function::function_buffer));  
      |           ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/test/unit_test.hpp:19:  
mwe.cpp: In function ‘(static initializers for mwe.cpp)’:  
mwe.cpp:9:71: note: ‘<anonymous>’ declared here  
    9 | BOOST_TEST_DECORATOR(*boost::unit_test::precondition(my_precondition()))  
      |                                                                       ^  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-05-30 17:55:52 UTC  
> Url: https://github.com/boostorg/function/issues/42#issuecomment-1141382485  

Fixed by a variation of #43.

---

## Comment 2

> Username: jngrad  
> Created at: 2022-05-30 18:18:15 UTC  
> Url: https://github.com/boostorg/function/issues/42#issuecomment-1141395748  

Fixed by 5b4e2797a28f9477d2df5fa915409dac73ff00c6.
