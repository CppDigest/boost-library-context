# #3019 Fix uninitialized variable [Merged]

> Username: dzlaus  
> Created at: 2025-07-14 19:12:59 UTC  
> Updated at: 2025-07-20 18:25:59 UTC  
> Merged at: 2025-07-19 16:53:55 UTC  
> Closed at: 2025-07-19 16:53:55 UTC  
> Url: https://github.com/boostorg/beast/pull/3019  

Have run into a very peculiar error on GCC 15.1 which only manifests when using with the following flags   
'-Wall Werror=uninitialized -fhardened' and only under Release mode compilation.  
  
The -fhardened flag is critical, as without it the compiler will happily compile the code. No idea what trips the compiler, but the fix is trivial.  
  
The issue is reproducible on the [WebSocket server, asynchronous](https://www.boost.org/doc/libs/1_88_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp) example.  
  
The full compiler output is as follows:  
  
```  
ninja  
[1/2] Building CXX object CMakeFiles/unitialized_variable.dir/main.cpp.o  
FAILED: CMakeFiles/unitialized_variable.dir/main.cpp.o   
/usr/lib/ccache/bin/g++   -O3 -DNDEBUG -std=gnu++26 -fdiagnostics-color=always -Wall -Werror=uninitialized -fhardened -MD -MT CMakeFiles/unitialized_variable.dir/main.cpp.o -MF CMakeFiles/unitialized_variable.dir/main.cpp.o.d -o CMakeFiles/unitialized_variable.dir/main.cpp.o -c /home/user/workspace/unitialized_variable/main.cpp  
In file included from /usr/include/boost/endian/detail/endian_reverse.hpp:9,  
                 from /usr/include/boost/endian/conversion.hpp:11,  
                 from /usr/include/boost/beast/websocket/detail/frame.hpp:20,  
                 from /usr/include/boost/beast/websocket/detail/impl_base.hpp:14,  
                 from /usr/include/boost/beast/websocket/stream.hpp:20,  
                 from /usr/include/boost/beast/websocket.hpp:18,  
                 from /home/user/workspace/unitialized_variable/main.cpp:17:  
In function ‘constexpr uint16_t boost::endian::detail::endian_reverse_impl(uint16_t)’,  
    inlined from ‘constexpr typename std::enable_if<(! std::is_class<_Tp>::value), T>::type boost::endian::endian_reverse(T) [with T = short unsigned int]’ at /usr/include/boost/endian/detail/endian_reverse.hpp:131:55,  
    inlined from ‘constexpr EndianReversible boost::endian::detail::conditional_reverse_impl(EndianReversible, std::false_type) [with EndianReversible = short unsigned int]’ at /usr/include/boost/endian/conversion.hpp:181:26,  
    inlined from ‘constexpr EndianReversible boost::endian::conditional_reverse(EndianReversible) [with order From = boost::endian::order::big; order To = boost::endian::order::little; EndianReversible = short unsigned int]’ at /usr/include/boost/endian/conversion.hpp:191:44,  
    inlined from ‘constexpr EndianReversible boost::endian::big_to_native(EndianReversible) [with EndianReversible = short unsigned int]’ at /usr/include/boost/endian/conversion.hpp:148:73,  
    inlined from ‘void boost::beast::websocket::detail::read_close(boost::beast::websocket::close_reason&, const Buffers&, boost::beast::error_code&) [with Buffers = boost::beast::buffers_prefix_view<boost::beast::detail::buffers_pair<true> >]’ at /usr/include/boost/beast/websocket/detail/frame.hpp:224:36:  
/usr/include/boost/endian/detail/endian_reverse.hpp:60:12: error: ‘code_be’ may be used uninitialized [-Werror=maybe-uninitialized]  
   60 |     return BOOST_ENDIAN_INTRINSIC_BYTE_SWAP_2(x);  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/beast/websocket/detail/frame.hpp: In function ‘void boost::beast::websocket::detail::read_close(boost::beast::websocket::close_reason&, const Buffers&, boost::beast::error_code&) [with Buffers = boost::beast::buffers_prefix_view<boost::beast::detail::buffers_pair<true> >]’:  
/usr/include/boost/beast/websocket/detail/frame.hpp:216:19: note: ‘code_be’ was declared here  
  216 |     std::uint16_t code_be;  
      |                   ^~~~~~~  
cc1plus: some warnings being treated as errors  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-07-14 20:44:10 UTC  
> Updated_at: 2025-07-16 15:20:45 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3070922077  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3019?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.11%. Comparing base [(`03f0ff3`)](https://app.codecov.io/gh/boostorg/beast/commit/03f0ff33cdd45ef6861f969268f9d17e1ced1249?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`44bc18a`)](https://app.codecov.io/gh/boostorg/beast/commit/44bc18a97c1ff3be8caff193aba8fb7032d314de?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3019/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3019?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #3019   +/-   ##  
========================================  
  Coverage    93.11%   93.11%             
========================================  
  Files          176      176             
  Lines        13643    13647    +4       
========================================  
+ Hits         12704    12708    +4       
  Misses         939      939             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3019?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/frame.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3019?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fframe.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3019?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fstream_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.21% <100.00%> (+0.02%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3019?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3019?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [03f0ff3...44bc18a](https://app.codecov.io/gh/boostorg/beast/pull/3019?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-07-15 10:24:54 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3073057532  

The following is minimal reproducible example of the same warning:  
https://godbolt.org/z/9axKqv1PP  
```C++  
#include <boost/beast.hpp>  
  
using namespace boost;  
  
std::uint16_t  
fun(  
    asio::const_buffer const& cb,  
    system::error_code& ec)  
{  
    if(cb.size() < 2)  
        return 0;  
  
    ec = {}; // commenting this line resolves warning  
  
    std::uint16_t result;  
    asio::buffer_copy(  
        asio::mutable_buffer(&result, sizeof(result)),  
        cb);  
    return endian::big_to_native(result);  
}  
  
```   
It seems the compiler thinks assigning to `ec` might change the buffer size.  
There are a few other places in the code with the same issue. Zero initializing the variable is a workaround, but ideally, we want the compiler to reason about the code, since logically a zero initialized variable is not what we want either.

---

## Comment 3

> Username: ashtum  
> Created_at: 2025-07-17 05:37:30 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3082611621  

For the record, I couldn't get the compiler to reason correctly about `asio::buffer_copy`. I used statements similar to `if (buffer_bytes(bs) < 2) __builtin_unreachable();` to provide additional information to the compiler. While this worked in a few cases, it failed in two cases in `stream_impl.hpp`. Therefore, it seems more consistent to zero-initialize all of them instead.  
https://godbolt.org/z/54GbYbr4a  
```C++  
#include <boost/beast.hpp>  
  
using namespace boost;  
  
std::uint16_t  
fun(  
    asio::const_buffer const& cb,  
    system::error_code& ec)  
{  
    if(cb.size() < 2)  
        return 0;  
  
    ec = {};  
    if(beast::buffer_bytes(cb) < 2) __builtin_unreachable();  
  
    std::uint16_t result;  
    asio::buffer_copy(  
        asio::mutable_buffer(&result, sizeof(result)),  
        cb);  
    return endian::big_to_native(result);  
}  
```

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-07-17 15:39:59 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3084544566  

An automated preview of the documentation is available at [https://3019.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://3019.beast.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 5

> Username: ashtum  
> Created_at: 2025-07-20 12:18:20 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3094484379  

@dzlaus Thank you for the PR. It has been merged into `master` and will be included in Boost 1.89.

---

## Comment 6

> Username: dzlaus  
> Created_at: 2025-07-20 18:25:59 UTC  
> Url: https://github.com/boostorg/beast/pull/3019#issuecomment-3094692169  

@ashtum Thanks for extending the patch to cover additional sites. I only uncovered the one in the original pull request.   
  
Tried to analyse a binary diff of the code gen when the variable is left uninitialized or `{}` initialized to see if the compiler is perhaps complaining due to how it allocates registers to variables, but did not see anything obvious.

---
