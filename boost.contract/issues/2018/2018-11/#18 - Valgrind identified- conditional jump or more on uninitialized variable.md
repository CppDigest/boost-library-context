# #18 - Valgrind identified: conditional jump or more on uninitialized variable [Closed]

> Username: jeking3  
> Created at: 2018-11-05 01:08:11 UTC  
> Updated at: 2019-06-04 12:27:03 UTC  
> Closed at: 2019-01-08 19:09:23 UTC  
> Url: https://github.com/boostorg/contract/issues/18  

https://travis-ci.org/jeking3/contract/jobs/450610144#L2582  
```  
====== BEGIN OUTPUT ======  
==12898== Memcheck, a memory error detector  
==12898== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==12898== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==12898== Command: ../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union  
==12898==   
==12898== Conditional jump or move depends on uninitialised value(s)  
==12898==    at 0x40AB53: positive::invariant() const (union.cpp:24)  
==12898==    by 0x40AAB3: void boost::contract::access::const_invariant<positive>(positive const*) (access.hpp:157)  
==12898==    by 0x40A82B: boost::enable_if<boost::contract::access::has_const_invariant<positive>, void>::type boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_const_inv<positive>() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x40A718: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_inv(bool, bool, bool) (cond_inv.hpp:132)  
==12898==    by 0x40A58F: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_exit_all_inv() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x409DBF: boost::contract::detail::constructor<positive>::~constructor() (constructor.hpp:82)  
==12898==    by 0x409E58: boost::contract::detail::constructor<positive>::~constructor() (constructor.hpp:64)  
==12898==    by 0x40CA3D: boost::contract::detail::auto_ptr<boost::contract::detail::cond_base>::~auto_ptr() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x407E14: boost::contract::check::~check() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x404E00: positive::positive(int) (union.cpp:43)  
==12898==    by 0x404383: main (union.cpp:122)  
==12898==   
==12898== Conditional jump or move depends on uninitialised value(s)  
==12898==    at 0x40AB53: positive::invariant() const (union.cpp:24)  
==12898==    by 0x40AAB3: void boost::contract::access::const_invariant<positive>(positive const*) (access.hpp:157)  
==12898==    by 0x40A82B: boost::enable_if<boost::contract::access::has_const_invariant<positive>, void>::type boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_const_inv<positive>() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x40A765: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_inv(bool, bool, bool) (cond_inv.hpp:136)  
==12898==    by 0x40F81F: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_entry_inv() (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x40DD98: boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive>::exec_and(boost::contract::virtual_::action_enum, void (boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive>::*)()) (cond_subcontracting.hpp:349)  
==12898==    by 0x40F764: boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive>::check_subcontracted_entry_inv() (cond_subcontracting.hpp:197)  
==12898==    by 0x40D40B: boost::contract::detail::public_function<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive>::init() (public_function.hpp:86)  
==12898==    by 0x40CA09: boost::contract::detail::cond_base::initialize() (cond_base.hpp:55)  
==12898==    by 0x407D81: boost::contract::check::check(boost::contract::specify_except const&) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union)  
==12898==    by 0x404F8F: positive::get(int&) (union.cpp:60)  
==12898==    by 0x4043DB: main (union.cpp:124)  
==12898==   
==12898==   
==12898== HEAP SUMMARY:  
==12898==     in use at exit: 72,704 bytes in 1 blocks  
==12898==   total heap usage: 52 allocs, 51 frees, 79,744 bytes allocated  
==12898==   
==12898== LEAK SUMMARY:  
==12898==    definitely lost: 0 bytes in 0 blocks  
==12898==    indirectly lost: 0 bytes in 0 blocks  
==12898==      possibly lost: 0 bytes in 0 blocks  
==12898==    still reachable: 72,704 bytes in 1 blocks  
==12898==         suppressed: 0 bytes in 0 blocks  
==12898== Rerun with --leak-check=full to see details of leaked memory  
==12898==   
==12898== For counts of detected and suppressed errors, rerun with: -v  
==12898== Use --track-origins=yes to see where uninitialised values come from  
==12898== ERROR SUMMARY: 4 errors from 2 contexts (suppressed: 0 from 0)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/contract/build/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
    valgrind "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union"   > "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.output" "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/visibility-hidden/features-union.run...  
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-12-22 13:54:58 UTC  
> Updated at: 2018-12-22 13:55:21 UTC  
> Url: https://github.com/boostorg/contract/issues/18#issuecomment-449572005  

I need to look more into this, but at first glance it seems to be limited to the union.cpp example code and not an issue within the library code itself (also because it only shows with that example and the library APIs to program contracts for unions are the same as for classes and structs so if it was a library issue it will show in other tests and examples).  
  
I think the problem might be that the 2 constructors of the "positive" class in union.cpp do not initialize the i_ and x_ data members in the constructor initialization lists (they do the initialization in the constructor body but that is after the body already calls boost::contract::constructor). I should try to change them to:  
  
    explicit positive(int x) : i_(), d_() { ...}  
    explicit positive(double x) : i_(), d_() { ... }

---

## Comment 2

> Username: jeking3  
> Created at: 2019-01-08 19:09:23 UTC  
> Url: https://github.com/boostorg/contract/issues/18#issuecomment-452416067  

I re-tested develop and the issue no longer shows up in valgrind.  Closing.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-06-04 12:27:03 UTC  
> Url: https://github.com/boostorg/contract/issues/18#issuecomment-498649843  

This is an issue again:  
  
```  
testing.capture-output ../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run  
====== BEGIN OUTPUT ======  
==21734== Memcheck, a memory error detector  
==21734== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==21734== Using Valgrind-3.14.0 and LibVEX; rerun with -h for copyright info  
==21734== Command: ../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union  
==21734==   
==21734== Conditional jump or move depends on uninitialised value(s)  
==21734==    at 0x40B253: positive::invariant() const (union.cpp:24)  
==21734==    by 0x40B1B3: void boost::contract::access::const_invariant<positive>(positive const*) (access.hpp:157)  
==21734==    by 0x40AF2B: boost::enable_if<boost::contract::access::has_const_invariant<positive>, void>::type boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_const_inv<positive>() (cond_inv.hpp:154)  
==21734==    by 0x40AE18: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_inv(bool, bool, bool) (cond_inv.hpp:132)  
==21734==    by 0x40AC8F: boost::contract::detail::cond_inv<boost::contract::detail::none, positive>::check_exit_all_inv() (cond_inv.hpp:114)  
==21734==    by 0x40A4AF: boost::contract::detail::constructor<positive>::~constructor() (constructor.hpp:83)  
==21734==    by 0x40A548: boost::contract::detail::constructor<positive>::~constructor() (constructor.hpp:65)  
==21734==    by 0x40D25D: boost::contract::detail::auto_ptr<boost::contract::detail::cond_base>::~auto_ptr() (auto_ptr.hpp:23)  
==21734==    by 0x4085A4: boost::contract::check::~check() (check.hpp:330)  
==21734==    by 0x405170: positive::positive(int) (union.cpp:44)  
==21734==    by 0x40462C: main (union.cpp:123)  
==21734==   
==21734== Conditional jump or move depends on uninitialised value(s)  
==21734==    at 0x40B253: positive::invariant() const (union.cpp:24)  
==21734==    by 0x40B1B3: void boost::contract::access::const_invariant<positive>(positive const*) (access.hpp:157)  
==21734==    by 0x40E94B: boost::enable_if<boost::contract::access::has_const_invariant<positive const>, void>::type boost::contract::detail::cond_inv<boost::contract::detail::none, positive const>::check_const_inv<positive const>() (cond_inv.hpp:154)  
==21734==    by 0x40E885: boost::contract::detail::cond_inv<boost::contract::detail::none, positive const>::check_inv(bool, bool, bool) (cond_inv.hpp:136)  
==21734==    by 0x4103BF: boost::contract::detail::cond_inv<boost::contract::detail::none, positive const>::check_entry_inv() (cond_inv.hpp:106)  
==21734==    by 0x40E678: boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive const>::exec_and(boost::contract::virtual_::action_enum, void (boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive const>::*)()) (cond_subcontracting.hpp:352)  
==21734==    by 0x410304: boost::contract::detail::cond_subcontracting<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive const>::check_subcontracted_entry_inv() (cond_subcontracting.hpp:200)  
==21734==    by 0x40DC2B: boost::contract::detail::public_function<boost::contract::detail::none, boost::contract::detail::none, boost::contract::detail::none, positive const>::init() (public_function.hpp:87)  
==21734==    by 0x40D229: boost::contract::detail::cond_base::initialize() (cond_base.hpp:55)  
==21734==    by 0x408511: boost::contract::check::check(boost::contract::specify_except const&) (check.hpp:271)  
==21734==    by 0x4052FF: positive::get(int&) const (union.cpp:61)  
==21734==    by 0x40468B: main (union.cpp:125)  
==21734==   
==21734==   
==21734== HEAP SUMMARY:  
==21734==     in use at exit: 0 bytes in 0 blocks  
==21734==   total heap usage: 54 allocs, 54 frees, 80,112 bytes allocated  
==21734==   
==21734== All heap blocks were freed -- no leaks are possible  
==21734==   
==21734== For counts of detected and suppressed errors, rerun with: -v  
==21734== Use --track-origins=yes to see where uninitialised values come from  
==21734== ERROR SUMMARY: 4 errors from 2 contexts (suppressed: 0 from 0)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/contract/build/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
    valgrind "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union"   > "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output" "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/contract/example/features-union.test/clang-linux-6.0/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run...  
```  
  
https://travis-ci.org/jeking3/contract/jobs/541174229#L3827
