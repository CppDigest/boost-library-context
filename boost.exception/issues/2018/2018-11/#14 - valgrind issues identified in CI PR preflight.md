# #14 - valgrind issues identified in CI PR preflight [Closed]

> Username: jeking3  
> Created at: 2018-11-12 21:43:01 UTC  
> Updated at: 2018-11-14 02:30:16 UTC  
> Closed at: 2018-11-14 00:15:48 UTC  
> Url: https://github.com/boostorg/exception/issues/14  

Build command:  
  
VALGRIND_OPTS=--error-exitcode=1 /home/travis/build/jeking3/boost-root/b2 . toolset=clang-6.0 cxxstd=03,11,14,17,2a define=BOOST_NO_STRESS_TEST=1 testing.launcher=valgrind variant=debug -j3  
  
This error appears to occurs in the same test 5 times, once for each language level that was run, so I copied in the first occurrence:  
  
https://travis-ci.org/jeking3/exception/jobs/454168879#L1188  
```  
====== BEGIN OUTPUT ======  
==7746== Memcheck, a memory error detector  
==7746== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==7746== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==7746== Command: ../../bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test  
==7746==   
==7746== Use of uninitialised value of size 8  
==7746==    at 0x4EE2D0E: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x4EE3714: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<unsigned long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, unsigned long) const (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x4EEF154: std::ostream& std::ostream::_M_insert<unsigned long>(unsigned long) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x40429D: std::string boost::exception_detail::object_hex_dump<boost::to_string_tester>(boost::to_string_tester const&, unsigned long) (object_hex_dump.hpp:40)  
==7746==    by 0x403FA1: std::string boost::exception_detail::string_stub_dump<boost::to_string_tester>(boost::to_string_tester const&) (to_string_stub.hpp:86)  
==7746==    by 0x404070: std::string boost::exception_detail::to_string_dispatcher<false>::convert<boost::to_string_tester, std::string (*)(boost::to_string_tester const&)>(boost::to_string_tester const&, std::string (*)(boost::to_string_tester const&)) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x403F63: std::string boost::exception_detail::to_string_dispatch::dispatch<boost::to_string_tester, std::string (*)(boost::to_string_tester const&)>(boost::to_string_tester const&, std::string (*)(boost::to_string_tester const&)) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x402265: std::string boost::to_string_stub<boost::to_string_tester>(boost::to_string_tester const&) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x401C26: main (to_string_stub_test.cpp:96)  
==7746==   
==7746== Conditional jump or move depends on uninitialised value(s)  
==7746==    at 0x4EE2D15: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x4EE3714: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<unsigned long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, unsigned long) const (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x4EEF154: std::ostream& std::ostream::_M_insert<unsigned long>(unsigned long) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==7746==    by 0x40429D: std::string boost::exception_detail::object_hex_dump<boost::to_string_tester>(boost::to_string_tester const&, unsigned long) (object_hex_dump.hpp:40)  
==7746==    by 0x403FA1: std::string boost::exception_detail::string_stub_dump<boost::to_string_tester>(boost::to_string_tester const&) (to_string_stub.hpp:86)  
==7746==    by 0x404070: std::string boost::exception_detail::to_string_dispatcher<false>::convert<boost::to_string_tester, std::string (*)(boost::to_string_tester const&)>(boost::to_string_tester const&, std::string (*)(boost::to_string_tester const&)) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x403F63: std::string boost::exception_detail::to_string_dispatch::dispatch<boost::to_string_tester, std::string (*)(boost::to_string_tester const&)>(boost::to_string_tester const&, std::string (*)(boost::to_string_tester const&)) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x402265: std::string boost::to_string_stub<boost::to_string_tester>(boost::to_string_tester const&) (in /home/travis/build/jeking3/boost-root/bin.v2/libs/exception/test/to_string_stub_test.test/clang-linux-6.0/debug/cxxstd-03-iso/link-static/visibility-hidden/to_string_stub_test)  
==7746==    by 0x401C26: main (to_string_stub_test.cpp:96)  
==7746==   
No errors detected.  
==7746==   
==7746== HEAP SUMMARY:  
==7746==     in use at exit: 72,704 bytes in 1 blocks  
==7746==   total heap usage: 20 allocs, 19 frees, 75,987 bytes allocated  
==7746==   
==7746== LEAK SUMMARY:  
==7746==    definitely lost: 0 bytes in 0 blocks  
==7746==    indirectly lost: 0 bytes in 0 blocks  
==7746==      possibly lost: 0 bytes in 0 blocks  
==7746==    still reachable: 72,704 bytes in 1 blocks  
==7746==         suppressed: 0 bytes in 0 blocks  
==7746== Rerun with --leak-check=full to see details of leaked memory  
==7746==   
==7746== For counts of detected and suppressed errors, rerun with: -v  
==7746== Use --track-origins=yes to see where uninitialised values come from  
==7746== ERROR SUMMARY: 4 errors from 2 contexts (suppressed: 0 from 0)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: zajo  
> Created at: 2018-11-14 00:15:48 UTC  
> Url: https://github.com/boostorg/exception/issues/14#issuecomment-438488648  

These are probably bogus. I took a fresh look at the implementation of `object_hex_dump` and I don't see a problem. The point of `object_hex_dump` is to print the first up to 16 bytes of the object's memory, in `diagnostic_information` output, in case the object doesn't provide a way to be converted to a string.  
  
I'm guessing that VALGRIND is just warning that the memory being accessed isn't initialized, which is fine, because it is part of a valid C++ object (which is trivially constructible).  
  
Let me know if you still think this is an error.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-14 02:30:16 UTC  
> Url: https://github.com/boostorg/exception/issues/14#issuecomment-438514906  

It would be nice to annotate the code so valgrind can ignore it.  That or  
actually have the test initialize the object.   That way if something more  
serious popped up in a future PR it would not be in an "allowed failure"  
job and get ignored.  
  
On Tue, Nov 13, 2018, 7:15 PM Emil Dotchevski <notifications@github.com  
wrote:  
  
> These are probably bogus. I took a fresh look at the implementation of  
> object_hex_dump and I don't see a problem. The point of object_hex_dump  
> is to print the first up to 16 bytes of the object's memory, in  
> diagnostic_information output, in case the object doesn't provide a way  
> to be converted to a string.  
>  
> I'm guessing that VALGRIND is just warning that the memory being accessed  
> isn't initialized, which is fine, because it is part of a valid C++ object  
> (which is trivially constructible).  
>  
> Let me know if you still think this is an error.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/exception/issues/14#issuecomment-438488648>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbR-w6KbgjrzZcAEQayPRU2Xh5aHSks5uu2C0gaJpZM4YafMq>  
> .  
>
