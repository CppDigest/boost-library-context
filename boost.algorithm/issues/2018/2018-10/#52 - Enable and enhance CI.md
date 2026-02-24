# #52 - Enable and enhance CI [Closed]

> Username: jeking3  
> Created at: 2018-10-27 12:15:49 UTC  
> Updated at: 2022-06-24 04:14:22 UTC  
> Closed at: 2022-06-24 04:14:22 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52  

Travis and Appveyor files were added 5 months ago, but CI was never enabled for this repository.  
Add codecov.io, cppcheck, covscan, ubsan, and valgrind targets to improve quality.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-10-30 20:53:56 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-434465655  

@mclow I found an issue building with C++03:  
```  
clang-linux.compile.c++.without-pth ../../bin.v2/libs/algorithm/test/apply_permutation_test.test/clang-linux-6.0/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o  
In file included from test/apply_permutation_test.cpp:13:  
In file included from ../../boost/algorithm/apply_permutation.hpp:22:  
In file included from /usr/lib/gcc/x86_64-linux-gnu/7.3.0/../../../../include/c++/7.3.0/type_traits:35:  
/usr/lib/gcc/x86_64-linux-gnu/7.3.0/../../../../include/c++/7.3.0/bits/c++0x_warning.h:32:2: error: This file requires compiler and library support for the ISO C++ 2011 standard. This support must be enabled with the -std=c++11 or -std=gnu++11 compiler options.  
#error This file requires compiler and library support \  
 ^  
In file included from test/apply_permutation_test.cpp:13:  
../../boost/algorithm/apply_permutation.hpp:44:18: warning: alias declarations are a C++11 extension [-Wc++11-extensions]  
    using Diff = typename std::iterator_traits<RandomAccessIterator1>::difference_type;  
                 ^  
../../boost/algorithm/apply_permutation.hpp:49:9: warning: 'auto' type specifier is a C++11 extension [-Wc++11-extensions]  
        auto current = i;  
        ^  
../../boost/algorithm/apply_permutation.hpp:52:13: warning: 'auto' type specifier is a C++11 extension [-Wc++11-extensions]  
            auto next = ind_begin[current];  
            ^  
../../boost/algorithm/apply_permutation.hpp:78:18: warning: alias declarations are a C++11 extension [-Wc++11-extensions]  
    using Diff = typename std::iterator_traits<RandomAccessIterator2>::difference_type;  
                 ^  
test/apply_permutation_test.cpp:15:9: warning: 'BOOST_TEST_DYN_LINK' macro redefined [-Wmacro-redefined]  
#define BOOST_TEST_DYN_LINK  
        ^  
<command line>:5:9: note: previous definition is here  
#define BOOST_TEST_DYN_LINK 1  
        ^  
5 warnings and 1 error generated.  
  "clang++" -c -x c++ -std=c++03 -fvisibility-inlines-hidden -fPIC -m64 -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_DYN_LINK=1 -DNDEBUG -I"../.." -o "../../bin.v2/libs/algorithm/test/apply_permutation_test.test/clang-linux-6.0/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o" "test/apply_permutation_test.cpp"  
...failed clang-linux.compile.c++.without-pth ../../bin.v2/libs/algorithm/test/apply_permutation_test.test/clang-linux-6.0/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o...  
```

---

## Comment 2

> Username: mclow  
> Created at: 2018-10-30 21:21:13 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-434474356  

clearly that code is not going to work on C++03.  I'll see if it can be made to do so.

---

## Comment 3

> Username: mclow  
> Created at: 2018-10-30 22:24:30 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-434492143  

Fixed in develop.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-10-31 12:43:07 UTC  
> Updated at: 2018-10-31 12:48:51 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-434672252  

Still failing:  
```  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/apply_permutation_test.test/gcc-5/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o  
In file included from /usr/include/c++/5/type_traits:35:0,  
                 from ../../boost/algorithm/apply_permutation.hpp:22,  
                 from test/apply_permutation_test.cpp:13:  
/usr/include/c++/5/bits/c++0x_warning.h:32:2: error: #error This file requires compiler and library support for the ISO C++ 2011 standard. This support must be enabled with the -std=c++11 or -std=gnu++11 compiler options.  
 #error This file requires compiler and library support \  
  ^  
  
    "g++-5"   -std=c++03 -fvisibility-inlines-hidden -fPIC -m64 -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_DYN_LINK=1 -DNDEBUG  -I"../.." -c -o "../../bin.v2/libs/algorithm/test/apply_permutation_test.test/gcc-5/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o" "test/apply_permutation_test.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/algorithm/test/apply_permutation_test.test/gcc-5/release/cxxstd-03-iso/visibility-hidden/apply_permutation_test.o...  
```  
  
I will clean this up on my side and submit it with the PR.  Could you turn on CI for Travis and AppVeyor per the instructions in https://github.com/jeking3/boost-ci (specifically item #11, enable Travis CI and AppVeyor for the boostorg/algorithm project), or I can manage those for you, but I would need to be marked as admin on this repo to do that.  
  
I think the include for type_traits can just be removed...

---

## Comment 5

> Username: jeking3  
> Created at: 2018-10-31 13:49:20 UTC  
> Updated at: 2018-10-31 22:30:36 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-434693808  

@mclow clang 3.4, 3.8, 4.0, and 5.0 are failing in CI (clang 6.0 succeeds):  
https://travis-ci.org/jeking3/algorithm/builds/448832426  
  
The only change I made in that branch is to remove the `#include <type_traits>` as discussed above.  
  
Could you take a quick look and see if I did something wrong, or if it's a real issue?  
  
Full results are otherwise pretty good.    
  
All windows builds passed.  
https://ci.appveyor.com/project/jeking3/algorithm/builds/19945313  
  
The ubsan issue with gcc-8 on Boost.Test popped up again, and the clang 3.4, 3.8, 4.0, 5.0 issue can be seen:  
https://travis-ci.org/jeking3/algorithm/builds/448860898

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-02 12:28:38 UTC  
> Updated at: 2018-11-02 12:30:27 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435362603  

Looks like a capability mismatch in libstdc++ and code in algorithm, but gcc passes - however I see that with gcc 4.8 I use CXXSTD 03,11 when building, so I just have to tewak the build jobs.  
```  
clang-linux.compile.c++.without-pth ../../bin.v2/libs/algorithm/test/clamp_test.test/clang-linux-3.4/release/cxxstd-14-iso/visibility-hidden/clamp_test.o  
test/clamp_test.cpp:48:32: error: constexpr variable 'constexpr_res' must be initialized by a constant expression  
    BOOST_CXX14_CONSTEXPR bool constexpr_res = (  
                               ^               ~  
../../boost/algorithm/clamp.hpp:54:12: note: non-constexpr function 'operator()' cannot be used in a constant expression  
    return p ( val, lo ) ? lo : p ( hi, val ) ? hi : val;  
           ^  
../../boost/algorithm/clamp.hpp:75:12: note: in call to 'clamp(3, 1, 10, {{}})'  
    return boost::algorithm::clamp ( val, lo, hi, std::less<T>());  
           ^  
test/clamp_test.cpp:49:9: note: in call to 'clamp(3, 1, 10)'  
        ba::clamp (  3, 1, 10 ) == 3  
        ^  
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../include/c++/4.8/bits/stl_function.h:234:7: note: declared here  
      operator()(const _Tp& __x, const _Tp& __y) const  
      ^  
```

---

## Comment 7

> Username: jeking3  
> Created at: 2018-11-02 18:20:16 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435466533  

The only remaining issue is a UBSAN issue:  
```  
====== BEGIN OUTPUT ======  
../../boost/algorithm/hex.hpp:52:29: runtime error: member call on address 0x7fff2cddc050 which does not point to an object of type 'exception'  
0x7fff2cddc050: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  50 69 44 00 00 00 00 00  00 00 00 00 00 00 00 00  a0 b7 43 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x4218ca in boost::algorithm::not_enough_input::~not_enough_input() ../../boost/algorithm/hex.hpp:52  
    #1 0x42b2d4 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<boost::algorithm::not_enough_input>::type> boost::exception_detail::enable_both<boost::algorithm::not_enough_input>(boost::algorithm::not_enough_input const&) ../../boost/exception/exception.hpp:517  
    #2 0x429d5e in void boost::throw_exception<boost::algorithm::not_enough_input>(boost::algorithm::not_enough_input const&) ../../boost/throw_exception.hpp:70  
    #3 0x42428e in void boost::exception_detail::throw_exception_<boost::algorithm::not_enough_input>(boost::algorithm::not_enough_input const&, char const*, char const*, int) ../../boost/throw_exception.hpp:87  
    #4 0x421b4f in boost::enable_if<boost::is_integral<boost::algorithm::detail::hex_iterator_traits<std::back_insert_iterator<std::string> >::value_type>, std::back_insert_iterator<std::string> >::type boost::algorithm::detail::decode_one<char const*, std::back_insert_iterator<std::string>, bool (*)(char const*, char const*)>(char const*&, char const*, std::back_insert_iterator<std::string>, bool (*)(char const*, char const*)) ../../boost/algorithm/hex.hpp:131  
    #5 0x41d838 in std::back_insert_iterator<std::string> boost::algorithm::unhex<char, std::back_insert_iterator<std::string> >(char const*, std::back_insert_iterator<std::string>) ../../boost/algorithm/hex.hpp:265  
    #6 0x4154cc in void test_from_hex_failure<std::string>(std::string::value_type const**) test/hex_test1.cpp:110  
    #7 0x405397 in test_main::test_method() test/hex_test1.cpp:188  
    #8 0x404e9d in test_main_invoker test/hex_test1.cpp:183  
    #9 0x423fab in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:152  
    #10 0x7f299d737f6f in boost::function0<void>::operator()() const ../../boost/function/function_template.hpp:829  
    #11 0x7f299d7344a6 in boost::detail::forward::operator()() (/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden/libboost_unit_test_framework.so.1.69.0+0x2804a6)  
    #12 0x7f299d73b87a in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:172  
    #13 0x7f299d7393bf in boost::function0<int>::operator()() const ../../boost/function/function_template.hpp:829  
    #14 0x7f299d73641d in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden/libboost_unit_test_framework.so.1.69.0+0x28241d)  
    #15 0x7f299d72c104 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:875  
    #16 0x7f299d72c62c in boost::execution_monitor::execute(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:1214  
    #17 0x7f299d72edd2 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ../../boost/test/impl/execution_monitor.ipp:1321  
    #18 0x7f299d81805f in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ../../boost/test/impl/unit_test_monitor.ipp:49  
    #19 0x7f299d763219 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden/libboost_unit_test_framework.so.1.69.0+0x2af219)  
    #20 0x7f299d762363 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden/libboost_unit_test_framework.so.1.69.0+0x2ae363)  
    #21 0x7f299d748cc8 in boost::unit_test::framework::run(unsigned long, bool) ../../boost/test/impl/framework.ipp:1631  
    #22 0x7f299d80eb35 in boost::unit_test::unit_test_main(bool (*)(), int, char**) ../../boost/test/impl/unit_test_main.ipp:247  
    #23 0x40485b in main ../../boost/test/unit_test.hpp:63  
    #24 0x7f299bebef44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #25 0x404788  (/home/travis/build/jeking3/boost-root/bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1+0x404788)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1"   > "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.output" "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/algorithm/test/hex_test1.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/hex_test1.run...  
```  
  
I am going to submit a PR for this work which will fail on the UBSAN, and needs addressing.

---

## Comment 8

> Username: mclow  
> Created at: 2018-11-04 19:07:48 UTC  
> Updated at: 2018-11-04 19:27:05 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435696607  

I've tried to reproduce the UBSAN failure locally (with clang and gcc 7.3), and failed. Here's my test case:  
  
	#include <stdexcept>  
  
	#include <boost/exception/exception.hpp>  
	#include <boost/throw_exception.hpp>  
  
	struct hex_decode_error : virtual boost::exception, virtual std::exception {};  
	struct not_enough_input : virtual hex_decode_error {};  
	struct non_hex_input    : virtual hex_decode_error {};  
  
	void toss() { BOOST_THROW_EXCEPTION(not_enough_input()); }  
  
	int main () {  
		try { toss(); }  
		catch ( const hex_decode_error & /*ex*/ ) {}  
		}

---

## Comment 9

> Username: jeking3  
> Created at: 2018-11-04 21:49:30 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435711111  

It is happening with gcc-8 ubsan however.

---

## Comment 10

> Username: mclow  
> Created at: 2018-11-04 22:09:37 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435712672  

Can you reproduce it with my sample program? (I won't have a chance to build gcc 8 until next weekend).

---

## Comment 11

> Username: jeking3  
> Created at: 2018-11-04 22:52:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435716028  

Running it through CI now, will post results.

---

## Comment 12

> Username: jeking3  
> Created at: 2018-11-05 13:11:39 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-435868574  

Still failed.  Sounds silly, but I wonder if this is because a virtual destructor is not defined?  
https://travis-ci.org/jeking3/algorithm/jobs/450656065  
https://api.travis-ci.org/v3/job/450656065/log.txt  
```  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.o  
gcc.link ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow  
testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.run  
====== BEGIN OUTPUT ======  
test/mclow.cpp:7:8: runtime error: member call on address 0x7ffdb8d73470 which does not point to an object of type 'exception'  
0x7ffdb8d73470: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  70 dc 40 00 00 00 00 00  00 00 00 00 00 00 00 00  48 b1 40 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x406359 in not_enough_input::~not_enough_input() test/mclow.cpp:7  
    #1 0x407666 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<not_enough_input>::type> boost::exception_detail::enable_both<not_enough_input>(not_enough_input const&) ../../boost/exception/exception.hpp:517  
    #2 0x407290 in void boost::throw_exception<not_enough_input>(not_enough_input const&) ../../boost/throw_exception.hpp:70  
    #3 0x40303d in void boost::exception_detail::throw_exception_<not_enough_input>(not_enough_input const&, char const*, char const*, int) ../../boost/throw_exception.hpp:87  
    #4 0x401367 in toss() test/mclow.cpp:10  
    #5 0x401415 in main test/mclow.cpp:13  
    #6 0x7fd6d0055f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #7 0x401238  (/home/travis/build/jeking3/boost-root/bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow+0x401238)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-03-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow"   > "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.output" "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/mclow.run...  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.o  
gcc.link ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow  
testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.run  
====== BEGIN OUTPUT ======  
test/mclow.cpp:7:8: runtime error: member call on address 0x7ffe25768f60 which does not point to an object of type 'exception'  
0x7ffe25768f60: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  70 dc 40 00 00 00 00 00  00 00 00 00 00 00 00 00  68 b0 40 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x406275 in not_enough_input::~not_enough_input() test/mclow.cpp:7  
    #1 0x407582 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<not_enough_input>::type> boost::exception_detail::enable_both<not_enough_input>(not_enough_input const&) ../../boost/exception/exception.hpp:517  
    #2 0x4071ac in void boost::throw_exception<not_enough_input>(not_enough_input const&) ../../boost/throw_exception.hpp:70  
    #3 0x402f59 in void boost::exception_detail::throw_exception_<not_enough_input>(not_enough_input const&, char const*, char const*, int) ../../boost/throw_exception.hpp:87  
    #4 0x401307 in toss() test/mclow.cpp:10  
    #5 0x4013b5 in main test/mclow.cpp:13  
    #6 0x7fef9f72bf44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #7 0x4011d8  (/home/travis/build/jeking3/boost-root/bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow+0x4011d8)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-11-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-11-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-11-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-11-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow"   > "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.output" "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/mclow.run...  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.o  
gcc.link ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow  
testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.run  
====== BEGIN OUTPUT ======  
test/mclow.cpp:7:8: runtime error: member call on address 0x7ffc7d7c21a0 which does not point to an object of type 'exception'  
0x7ffc7d7c21a0: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  70 dc 40 00 00 00 00 00  00 00 00 00 00 00 00 00  a8 b0 40 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x4062a1 in not_enough_input::~not_enough_input() test/mclow.cpp:7  
    #1 0x4075b2 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<not_enough_input>::type> boost::exception_detail::enable_both<not_enough_input>(not_enough_input const&) ../../boost/exception/exception.hpp:517  
    #2 0x4071dc in void boost::throw_exception<not_enough_input>(not_enough_input const&) ../../boost/throw_exception.hpp:70  
    #3 0x402f85 in void boost::exception_detail::throw_exception_<not_enough_input>(not_enough_input const&, char const*, char const*, int) ../../boost/throw_exception.hpp:87  
    #4 0x401327 in toss() test/mclow.cpp:10  
    #5 0x4013d5 in main test/mclow.cpp:13  
    #6 0x7f182a705f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #7 0x4011f8  (/home/travis/build/jeking3/boost-root/bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow+0x4011f8)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-14-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-14-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-14-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-14-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow"   > "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.output" "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/mclow.run...  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.o  
gcc.compile.c++ ../../bin.v2/libs/algorithm/test/hex_test4.test/gcc-8/debug/cxxstd-2a-iso/visibility-hidden/hex_test4.o  
gcc.link ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow  
testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.run  
====== BEGIN OUTPUT ======  
test/mclow.cpp:7:8: runtime error: member call on address 0x7ffe816a3290 which does not point to an object of type 'exception'  
0x7ffe816a3290: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  70 dc 40 00 00 00 00 00  00 00 00 00 00 00 00 00  a8 b0 40 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x407a95 in not_enough_input::~not_enough_input() test/mclow.cpp:7  
    #1 0x405fd8 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<not_enough_input>::type> boost::exception_detail::enable_both<not_enough_input>(not_enough_input const&) ../../boost/exception/exception.hpp:517  
    #2 0x405c03 in void boost::throw_exception<not_enough_input>(not_enough_input const&) ../../boost/throw_exception.hpp:70  
    #3 0x402f85 in void boost::exception_detail::throw_exception_<not_enough_input>(not_enough_input const&, char const*, char const*, int) ../../boost/throw_exception.hpp:87  
    #4 0x401327 in toss() test/mclow.cpp:10  
    #5 0x4013d5 in main test/mclow.cpp:13  
    #6 0x7f70b8f5df44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #7 0x4011f8  (/home/travis/build/jeking3/boost-root/bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow+0x4011f8)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-17-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-17-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-17-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-17-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow"   > "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.output" "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/algorithm/test/mclow.test/gcc-8/debug/cxxstd-17-iso/visibility-hidden/mclow.run...  
```  
If you need help enabling CI on algorithm let me know.  There are instructions at:  
https://github.com/jeking3/boost-ci  
  
I wonder if we should consider moving that into boostorg...

---

## Comment 13

> Username: jeking3  
> Created at: 2018-11-12 20:41:44 UTC  
> Url: https://github.com/boostorg/algorithm/issues/52#issuecomment-438021614  

Is this an issue for Boost.Exception?  Perhaps I should ramp up a CI for that repo?
