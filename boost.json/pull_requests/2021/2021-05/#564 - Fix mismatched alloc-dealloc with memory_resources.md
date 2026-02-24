# #564 Fix mismatched alloc/dealloc with memory_resources [Closed]

> Username: Oipo  
> Created at: 2021-05-04 20:47:16 UTC  
> Updated at: 2021-05-22 20:59:27 UTC  
> Closed at: 2021-05-22 20:59:27 UTC  
> Url: https://github.com/boostorg/json/pull/564  

I noticed the following AddressSanitizer error when using boost json:  
```  
=================================================================  
==54836==ERROR: AddressSanitizer: new-delete-type-mismatch on 0x60300008d9d0 in thread T0:  
  object passed to delete has wrong type:  
  size of the allocated type:   6 bytes;  
  size of the deallocated type: 6 bytes.  
  alignment of the allocated type:   16 bytes;  
  alignment of the deallocated type: 1 bytes.  
    #0 0x7ffff76abaec in operator delete(void*, unsigned long, std::align_val_t) ../../../../gcc-10.3.0/libsanitizer/asan/asan_new_delete.cpp:190  
    #1 0x4086c2 in std::pmr::memory_resource::deallocate(void*, unsigned long, unsigned long) /opt/gcc/10/include/c++/10.3.0/memory_resource:105  
    #2 0x6a19f7 in boost::json::standalone::key_value_pair::~key_value_pair() /home/oipo-unencrypted/Programming/json/include/boost/json/value.hpp:3426  
    #3 0x9fc702 in boost::json::standalone::object::destroy(boost::json::standalone::key_value_pair*, boost::json::standalone::key_value_pair*) /home/oipo-unencrypted/Programming/json/include/boost/json/impl/object.ipp:820  
    #4 0x9fc557 in boost::json::standalone::object::destroy() /home/oipo-unencrypted/Programming/json/include/boost/json/impl/object.ipp:808  
    #5 0x9d1147 in boost::json::standalone::object::~object() /home/oipo-unencrypted/Programming/json/include/boost/json/impl/object.ipp:247  
    #6 0x9e49cc in boost::json::standalone::value::~value() /home/oipo-unencrypted/Programming/json/include/boost/json/impl/value.ipp:43  
    #7 0x6991dc in boost::json::standalone::memory_resource_test::testPmr() /home/oipo-unencrypted/Programming/json/test/memory_resource.cpp:132  
    #8 0x699735 in boost::json::standalone::memory_resource_test::run() /home/oipo-unencrypted/Programming/json/test/memory_resource.cpp:144  
    #9 0x699eae in test_suite::detail::instance<boost::json::standalone::memory_resource_test>::run() /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:578  
    #10 0x6836df in test_suite::detail::runner::run(test_suite::detail::suite_info const&) /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:271  
    #11 0x68a33d in test_suite::run(std::ostream&, int, char const* const*) /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:703  
    #12 0x681db1 in main /home/oipo-unencrypted/Programming/json/test/main.cpp:30  
    #13 0x7ffff67580b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
    #14 0x407e0d in _start (/home/oipo-unencrypted/Programming/json/build2/test/boost_json-tests+0x407e0d)  
  
0x60300008d9d0 is located 0 bytes inside of 6-byte region [0x60300008d9d0,0x60300008d9d6)  
allocated by thread T0 here:  
    #0 0x7ffff76aaaef in operator new(unsigned long, std::align_val_t) ../../../../gcc-10.3.0/libsanitizer/asan/asan_new_delete.cpp:111  
    #1 0x4084fd in std::pmr::memory_resource::allocate(unsigned long, unsigned long) /opt/gcc/10/include/c++/10.3.0/memory_resource:100  
    #2 0x968b44 in boost::json::standalone::key_value_pair::key_value_pair<boost::json::standalone::value, boost::json::standalone::storage_ptr&>(std::basic_string_view<char, std::char_traits<char> >, boost::json::standalone::value&&, boost::json::standalone::storage_ptr&) /home/oipo-unencrypted/Programming/json/include/boost/json/value.hpp:3558  
    #3 0x9619ae in std::pair<boost::json::standalone::key_value_pair*, bool> boost::json::standalone::object::emplace<boost::json::standalone::value>(std::basic_string_view<char, std::char_traits<char> >, boost::json::standalone::value&&) /home/oipo-unencrypted/Programming/json/include/boost/json/impl/object.hpp:407  
    #4 0x9ee09d in boost::json::standalone::value_ref::make_object(std::initializer_list<boost::json::standalone::value_ref>, boost::json::standalone::storage_ptr) /home/oipo-unencrypted/Programming/json/include/boost/json/impl/value_ref.ipp:136  
    #5 0x9e66e9 in boost::json::standalone::value::value(std::initializer_list<boost::json::standalone::value_ref>, boost::json::standalone::storage_ptr) /home/oipo-unencrypted/Programming/json/include/boost/json/impl/value.ipp:177  
    #6 0x9e7010 in boost::json::standalone::value::operator=(std::initializer_list<boost::json::standalone::value_ref>) /home/oipo-unencrypted/Programming/json/include/boost/json/impl/value.ipp:213  
    #7 0x698f7b in boost::json::standalone::memory_resource_test::testPmr() /home/oipo-unencrypted/Programming/json/test/memory_resource.cpp:133  
    #8 0x699735 in boost::json::standalone::memory_resource_test::run() /home/oipo-unencrypted/Programming/json/test/memory_resource.cpp:144  
    #9 0x699eae in test_suite::detail::instance<boost::json::standalone::memory_resource_test>::run() /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:578  
    #10 0x6836df in test_suite::detail::runner::run(test_suite::detail::suite_info const&) /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:271  
    #11 0x68a33d in test_suite::run(std::ostream&, int, char const* const*) /home/oipo-unencrypted/Programming/json/test/test_suite.hpp:703  
    #12 0x681db1 in main /home/oipo-unencrypted/Programming/json/test/main.cpp:30  
    #13 0x7ffff67580b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
  
SUMMARY: AddressSanitizer: new-delete-type-mismatch ../../../../gcc-10.3.0/libsanitizer/asan/asan_new_delete.cpp:190 in operator delete(void*, unsigned long, std::align_val_t)  
==54836==HINT: if you don't care about these errors you may set ASAN_OPTIONS=new_delete_type_mismatch=0  
==54836==ABORTING  
```  
  
Which happened when using the `std::pmr::new_delete_resource()`. Fixed by removing the `sizeof(char)` parameter on deallocation.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-04 21:29:21 UTC  
> Updated_at: 2021-05-04 21:31:24 UTC  
> Url: https://github.com/boostorg/json/pull/564#issuecomment-832260900  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#564](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fd894a7) into [develop](https://codecov.io/gh/boostorg/json/commit/5e69b5a61e58140ea66583e8229e6b1938eee034?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5e69b5a) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/564/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #564   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6058     6058             
========================================  
  Hits          6004     6004             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/564/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `99.79% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5e69b5a...fd894a7](https://codecov.io/gh/boostorg/json/pull/564?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-05-04 22:29:04 UTC  
> Url: https://github.com/boostorg/json/pull/564#issuecomment-832290243  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/564/pullrequest-condensed-31eb706.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/564/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/564/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2021-05-22 20:59:27 UTC  
> Url: https://github.com/boostorg/json/pull/564#issuecomment-846462754  

The error is properly fixed in #572

---
