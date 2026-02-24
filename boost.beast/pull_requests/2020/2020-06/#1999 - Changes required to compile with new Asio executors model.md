# #1999 Changes required to compile with new Asio executors model [Closed]

> Username: madmongo1  
> Created at: 2020-06-26 12:46:07 UTC  
> Updated at: 2020-07-04 13:28:26 UTC  
> Closed at: 2020-07-04 13:28:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1999  

#refs 1994

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-26 13:40:33 UTC  
> Updated_at: 2020-07-03 16:08:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-650185397  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=h1) Report  
> Merging [#1999](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6f57e5934c1f307e4510ee11dd09594526a3f5d0&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1999/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1999      +/-   ##  
===========================================  
- Coverage    95.10%   95.09%   -0.02%       
===========================================  
  Files          152      153       +1       
  Lines        11960    11938      -22       
===========================================  
- Hits         11375    11352      -23       
- Misses         585      586       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `98.03% <ø> (-1.97%)` | :arrow_down: |  
| [include/boost/beast/core/impl/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYXN5bmNfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/saved\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc2F2ZWRfaGFuZGxlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/detail/get\_io\_context.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9nZXRfaW9fY29udGV4dC5ocHA=) | `94.73% <100.00%> (-0.27%)` | :arrow_down: |  
| [include/boost/beast/core/detail/work\_guard.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC93b3JrX2d1YXJkLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.88% <100.00%> (+0.01%)` | :arrow_up: |  
| ... and [4 more](https://codecov.io/gh/boostorg/beast/pull/1999/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=footer). Last update [6f57e59...f11bc01](https://codecov.io/gh/boostorg/beast/pull/1999?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2020-07-01 03:07:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652160489  

Remaining MSVC issues may be because I do not currently enable `BOOST_ASIO_HAS_WORKING_EXPRESSION_SFINAE` for any version of MSVC. (I just tried enabling it for a Visual Studio 2019 test runner, but it still triggers errors. They are different errors to earlier versions of MSVC though, so there may be workarounds.)  
  
For maximum portability you may need to define the relevant traits, as described in https://github.com/boostorg/beast/pull/1994#issuecomment-651123446.

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-07-01 06:22:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652217350  

> Remaining MSVC issues may be because I do not currently enable `BOOST_ASIO_HAS_WORKING_EXPRESSION_SFINAE` for any version of MSVC. (I just tried enabling it for a Visual Studio 2019 test runner, but it still triggers errors. They are different errors to earlier versions of MSVC though, so there may be workarounds.)  
>   
> For maximum portability you may need to define the relevant traits, as described in [#1994 (comment)](https://github.com/boostorg/beast/pull/1994#issuecomment-651123446).  
  
@chriskohlhoff Just to be clear, the errors seen in MSVC are when compiling vanilla asio types, not a custom executor type.

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-07-01 06:33:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652221214  

Example:  
  
```  
.\b2 variant=release cxxstd=14 toolset=msvc define=BOOST_ASIO_HAS_WORKING_EXPRESSION_SFINAE  -q libs/beast/test  
```  
  
output:  
```  
...  
    call "bin.v2\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\beast\test\beast\core.test\msvc-14.2\release\cxxstd-14-iso\threadapi-win32\threading-multi\core.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\beast\test\beast\core.test\msvc-14.2\release\cxxstd-14-iso\threadapi-win32\threading-multi\core.obj...  
compile-c-c++ bin.v2\libs\beast\test\beast\http.test\msvc-14.2\release\cxxstd-14-iso\threadapi-win32\threading-multi\http.obj  
http.cpp  
.\boost/asio/execution/blocking.hpp(364): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::possibly_t<0>'  
.\boost/asio/execution/blocking.hpp(209): note: see declaration of 'boost::asio::execution::detail::blocking::possibly_t<0>'  
.\boost/asio/execution/blocking.hpp(317): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
.\boost/asio/execution/blocking.hpp(374): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<I>' being compiled  
.\boost/asio/execution/blocking.hpp(365): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::always_t<0>'  
.\boost/asio/execution/blocking.hpp(210): note: see declaration of 'boost::asio::execution::detail::blocking::always_t<0>'  
.\boost/asio/execution/blocking.hpp(366): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::never_t<0>'  
.\boost/asio/execution/blocking.hpp(211): note: see declaration of 'boost::asio::execution::detail::blocking::never_t<0>'  
.\boost/asio/execution/blocking_adaptation.hpp(298): error C2027: use of undefined type 'boost::asio::execution::detail::blocking_adaptation::disallowed_t<0>'  
.\boost/asio/execution/blocking_adaptation.hpp(171): note: see declaration of 'boost::asio::execution::detail::blocking_adaptation::disallowed_t<0>'  
.\boost/asio/execution/blocking_adaptation.hpp(268): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_adaptation_t<0>' being compiled  
.\boost/asio/execution/blocking_adaptation.hpp(307): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_adaptation_t<I>' being compiled  
.\boost/asio/execution/blocking_adaptation.hpp(299): error C2027: use of undefined type 'boost::asio::execution::detail::blocking_adaptation::allowed_t<0>'  
.\boost/asio/execution/blocking_adaptation.hpp(172): note: see declaration of 'boost::asio::execution::detail::blocking_adaptation::allowed_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(369): error C2027: use of undefined type 'boost::asio::execution::detail::bulk_guarantee::unsequenced_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(210): note: see declaration of 'boost::asio::execution::detail::bulk_guarantee::unsequenced_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(320): note: see reference to class template instantiation 'boost::asio::execution::detail::bulk_guarantee_t<0>' being compiled  
.\boost/asio/execution/bulk_guarantee.hpp(379): note: see reference to class template instantiation 'boost::asio::execution::detail::bulk_guarantee_t<I>' being compiled  
.\boost/asio/execution/bulk_guarantee.hpp(370): error C2027: use of undefined type 'boost::asio::execution::detail::bulk_guarantee::sequenced_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(211): note: see declaration of 'boost::asio::execution::detail::bulk_guarantee::sequenced_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(371): error C2027: use of undefined type 'boost::asio::execution::detail::bulk_guarantee::parallel_t<0>'  
.\boost/asio/execution/bulk_guarantee.hpp(212): note: see declaration of 'boost::asio::execution::detail::bulk_guarantee  
  
-- More  --  
...etc...  
```  
  
This source file does not define any user-defined executors.

---

## Comment 5

> Username: madmongo1  
> Created_at: 2020-07-01 06:56:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652230291  

@chriskohlhoff here are other command lines that produce failures:  
  
```  
./b2  variant=release address-model=32 toolset=msvc-14.2  libs/beast/example  
./b2  cxxstd=11 variant=release address-model=32 toolset=msvc-14.2  libs/beast/example  
./b2  cxxstd=14 variant=release address-model=32 toolset=msvc-14.2  libs/beast/example  
./b2  cxxstd=17 variant=release address-model=32 toolset=msvc-14.2  libs/beast/example  
./b2  cxxstd=latest variant=release address-model=32 toolset=msvc-14.2  libs/beast/example  
```

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2020-07-01 07:25:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652243047  

Can you please post the contents of this file:  
  
> bin.v2\libs\beast\test\beast\core.test\msvc-14.2\release\cxxstd-14-iso\threadapi-win32\threading-multi\core.obj.rsp  
  
as it contains the actual command line options for the compiler.

---

## Comment 7

> Username: madmongo1  
> Created_at: 2020-07-01 07:48:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652254259  

> Can you please post the contents of this file:  
> bin.v2\libs\beast\test\beast\core.test\msvc-14.2\release\cxxstd-14-iso\threadapi-win32\threading-multi\core.obj.rsp  
> as it contains the actual command line options for the compiler.  
  
```  
"libs\beast\test\lib_test.cpp" -Fo"bin.v2\libs\beast\test\msvc-14.2\release\cxxstd-14-iso\link-static\threading-multi\lib_test.obj"    -TP /O2 /Ob2 /W3 /GR /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /Zc:throwingNew /wd4675 /EHs /std:c++14 -c  
-DBOOST_ALL_NO_LIB=1  
-DBOOST_BEAST_ALLOW_DEPRECATED  
-DBOOST_BEAST_SEPARATE_COMPILATION  
-DNDEBUG  
"-I."  
```

---

## Comment 8

> Username: chriskohlhoff  
> Created_at: 2020-07-02 02:50:59 UTC  
> Updated_at: 2020-07-02 03:35:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652749250  

For some reason that response file above is incomplete. Mine looks like:  
  
```  
"example\http\client\async\http_client_async.cpp" -Fo"..\..\bin.v2\libs\beast\example\http\client\async\msvc-14.2\release\cxxstd-14-iso\threading-multi\http_client_async.obj"    -TP /O2 /Ob2 /W3 /GR /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /Zc:throwingNew /wd4675 /EHs /std:c++14 /Ob2 /Oi /Ot /bigobj /permissive- -c   
-DBOOST_ALL_NO_LIB=1   
-DBOOST_ASIO_DISABLE_BOOST_ARRAY=1   
-DBOOST_ASIO_DISABLE_BOOST_BIND=1   
-DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1   
-DBOOST_ASIO_DISABLE_BOOST_REGEX=1   
-DBOOST_ASIO_NO_DEPRECATED=1   
-DBOOST_ASIO_SEPARATE_COMPILATION   
-DBOOST_BEAST_ALLOW_DEPRECATED   
-DBOOST_BEAST_SEPARATE_COMPILATION   
-DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1   
-DNDEBUG   
-D_CRT_SECURE_NO_WARNINGS=1   
-D_SCL_SECURE_NO_WARNINGS=1   
-D_SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING   
-D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING   
-D_WIN32_WINNT=0x0601   
"-I."   
"-I..\.."  
```  
  
The problem only occurs with the `/permissive-` option set. Unfortunately the errors don't really explain why this mode doesn't like the code, but perhaps you can disable this option for now.  
  
EDIT: I can enable a workaround in `asio/detail/config.hpp` to fix it. This workaround is already used for gcc <= 7.

---

## Comment 9

> Username: madmongo1  
> Created_at: 2020-07-02 07:43:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652843475  

> EDIT: I can enable a workaround in `asio/detail/config.hpp` to fix it. This workaround is already used for gcc <= 7.  
  
Oh. Thank goodness. From the documentation at https://docs.microsoft.com/en-us/cpp/build/reference/permissive-standards-conformance?view=vs-2019:  
  
> By default, the /permissive- option is set in new projects created by Visual Studio 2017 version 15.5 and later versions. It's not set by default in earlier versions. When the option is set, the compiler generates diagnostic errors or warnings when non-standard language constructs are detected in your code. These constructs include some common bugs in pre-C++11 code.  
  
Given the vast number of people who use the Visual studio IDEs I don't think requiring the disabling of this option is possible, let alone desirable.  
  
I'll test the latest commits and get back to you.

---

## Comment 10

> Username: madmongo1  
> Created_at: 2020-07-02 07:51:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652847270  

@chriskohlhoff Good news. It looks like it's building.  
  
FYI: Loads of warnings about very long template names (presumably unavoidable with all the new property code?)  
  
And you may want to look check this:  
  
```  
.\boost/asio/detail/impl/scheduler.ipp(365): warning C4267: '+=': conversion from 'size_t' to 'long', possible loss of data  
compile-c-c++ bin.v2\libs\chrono\build\msvc-14.0\release\address-model-64\cxxstd-14-iso\threading-multi\thread_clock.obj  
thread_clock.cpp  
```

---

## Comment 11

> Username: madmongo1  
> Created_at: 2020-07-02 08:01:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652852504  

@chriskohlhoff ok all looking good except for MSVC being unable to find specialisations for our custom executor. I have your instructions on how to fix that. Will report back when done.

---

## Comment 12

> Username: madmongo1  
> Created_at: 2020-07-02 08:16:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652860100  

@chriskohlhoff error:  
  
```  
C:\Users\hodge\github\boostorg\boost\boost/asio/execution/detail/as_invocable.hpp(80): error C2143: syntax error: missing ';' before '}'  
```  
  
Looks like the second #ifdef is one line too low:  
  
```  
#if !defined(BOOST_ASIO_NO_EXCEPTIONS)  
    try  
    {  
#endif // !defined(BOOST_ASIO_NO_EXCEPTIONS)  
      execution::set_value(BOOST_ASIO_MOVE_CAST(Receiver)(*receiver_));  
      receiver_ = 0;  
    }     //// <<<<==== spurious closing brace here  
#if !defined(BOOST_ASIO_NO_EXCEPTIONS)  
...  
```

---

## Comment 13

> Username: madmongo1  
> Created_at: 2020-07-02 08:32:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652868554  

https://github.com/boostorg/asio/pull/360

---

## Comment 14

> Username: madmongo1  
> Created_at: 2020-07-02 09:43:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652904738  

@chriskohlhoff still getting errors in msvc-14.1 and above  
  
```  
compile-c-c++ bin.v2\libs\beast\msvc-14.1\release\address-model-64\cxxstd-17-iso\link-static\threading-multi\test\lib_asio.obj  
lib_asio.cpp  
.\boost/asio/execution/blocking.hpp(377): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::possibly_t<0>'  
.\boost/asio/execution/blocking.hpp(222): note: see declaration of 'boost::asio::execution::detail::blocking::possibly_t<0>'  
.\boost/asio/execution/blocking.hpp(330): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
.\boost/asio/execution/blocking.hpp(387): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<I>' being compiled  
.\boost/asio/execution/blocking.hpp(378): error C2027: use of undefined type 'boost::asio::execution::detail::blocking::always_t<0>'  
.\boost/asio/execution/blocking.hpp(223): note: see declaration of 'boost::asio::execution::detail::blocking::always_t<0>'  
```

---

## Comment 15

> Username: chriskohlhoff  
> Created_at: 2020-07-02 09:49:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652907562  

N.B. I haven't merged these fixes into the boost source tree yet.

---

## Comment 16

> Username: chriskohlhoff  
> Created_at: 2020-07-02 10:49:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652934389  

Now on develop.

---

## Comment 17

> Username: madmongo1  
> Created_at: 2020-07-02 12:06:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-652966624  

@chriskohlhoff Tests passed for the "allow net-ts executors" case. Will get this PR tidied up and then work on the NO_TS_EXECUTORS version.  
Many thanks for your herculean efforts.

---

## Comment 18

> Username: madmongo1  
> Created_at: 2020-07-02 14:04:59 UTC  
> Updated_at: 2020-07-02 14:05:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653025847  

@chriskohlhoff bug in co_spawn:  
  
```  
In file included from /home/rhodges/github/boostorg/boost/libs/beast/example/http/server/fast/http_server_fast.cpp:21:  
In file included from /home/rhodges/github/boostorg/boost/boost/asio.hpp:47:  
In file included from /home/rhodges/github/boostorg/boost/boost/asio/co_spawn.hpp:469:  
/home/rhodges/github/boostorg/boost/boost/asio/impl/co_spawn.hpp:60:16: error: no template named 'executor_work_guard'; did you mean 'beast::detail::execution_work_guard'?  
    >::type> : executor_work_guard<Executor>  
               ^  
/home/rhodges/github/boostorg/boost/libs/beast/include/boost/beast/core/detail/work_guard.hpp:34:8: note: 'beast::detail::execution_work_guard' declared here  
struct execution_work_guard  
       ^  
```  
  
`clang++-9 -std=c++20 -fcoroutines-ts -DBOOST_ASIO_NO_TS_EXECUTORS`

---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:04:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441766790  

📁 CHANGELOG.md

```diff
   1 |+ * Deprecate asio invocation hooks
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:04:23 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449067799  

Handler invoke and allocation hooks are deprecated


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:10:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441773488  

📁 test/doc/core_1_refresher.cpp

```diff
 290 | {
 291 |-     using type = boost::asio::executor;
 291 |+     using type = boost::asio::any_io_executor;
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:10:17 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449071755  

This should be `net::any_io_executor`


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:10:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441773827  

📁 test/beast/websocket/ssl.cpp

```diff
  64 |                     boost::asio::ip::tcp,
  65 |-                     boost::asio::executor>>>();
  65 |+                     boost::asio::any_io_executor>>>();
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:10:30 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449071893  

`net::any_io_executor`


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:13:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441778603  

📁 test/beast/core/test_handler.hpp

```diff
 152 |             BEAST_EXPECT(hook_invoked);
 153 |         }
 154 |+ #endif // !defined(BOOST_ASIO_NO_DEPRECATED)
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:13:27 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449073710  

I don't put the trailing comment on ifdefs. It is too much hassle to repeat the expression, which is sometimes long and cumbersome. But that's just my preference.

> Username: madmongo1  
> Created_at: 2020-07-02 16:46:25 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449145476  

ok


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:16:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441783231  

📁 CHANGELOG.md

```diff
   1 |+ * Update bind_handler test to use standard executor form.
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:16:14 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449075556  

This is not really a user-visible change, and it is not necessary to mention it in the change log as it is already in the commit log.


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:16:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441783469  

📁 CHANGELOG.md

```diff
   1 |+ * Don't test deprecated hooks.
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:16:23 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449075646  

This is not really a user-visible change, and it is not necessary to mention it in the change log as it is already in the commit log.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:17:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441785818  

📁 include/boost/beast/core/async_base.hpp

```diff
 199 |+         net::associated_executor<
 200 |+             Handler,
 201 |+             typename detail::select_work_guard_t<Executor1>::executor_type
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:17:48 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449076599  

`detail` identifiers should not appear in the documentation, which will happen here


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:18:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441787284  

📁 include/boost/beast/core/detail/work_guard.hpp

```diff
  61 |+ private:
  62 |+ 
  63 |+     boost::optional<executor_type> exec_;
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:18:42 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449077170  

`ex` and `ex_` are the canonical names for variables that are executors


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:19:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441787999  

📁 include/boost/beast/core/detail/work_guard.hpp

```diff
  74 |+         || boost::asio::is_executor<Executor>::value
  75 |+ #else
  76 |+         && !boost::asio::is_executor<Executor>::value
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:19:09 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449077501  

Should these be `net::` instead of `boost::asio::`?


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:19:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441789143  

📁 test/beast/config.hpp

```diff
   9 |+ //
  10 |+ #ifndef BOOST_BEAST_TEST_CONFIG_HPP
  11 |+ #define BOOST_BEAST_TEST_CONFIG_HPP
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:19:49 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449077989  

Do we still need this file?


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441789479  

📁 test/beast/config.hpp

```diff
  14 |+ // Configures which tests will run and how
  15 |+ 
  16 |+ #include <boost/asio/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:00 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449078151  

You aren't allowed to include detail headers from other libraries


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441789792  

📁 test/beast/core/async_base.cpp

```diff
  25 | #include <boost/core/ignore_unused.hpp>
  26 | #include <stdexcept>
  27 |+ #include <boost/asio/execution/context_as.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:12 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449078290  

Asio includes should come right before std includes


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441790252  

📁 test/beast/core/async_base.cpp

```diff
  78 |+     { return false; }
  79 |+ };
  80 |+ static_assert(net::execution::can_execute<ex1_type, net::execution::invocable_archetype>::value, "");
```

> Username: vinniefalco  
> Created_at: 2020-07-02 15:20:28 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449078471  

Or you can use `BOOST_STATIC_ASSERT`


---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2020-07-02 16:56:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653119182  

An automated preview of the documentation is available at [http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2020-07-02 17:04:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653122737  

An automated preview of the documentation is available at [http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2020-07-02 17:08:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653124792  

An automated preview of the documentation is available at [http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2020-07-02 17:29:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653133690  

An automated preview of the documentation is available at [http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 17:46:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441905519  

📁 doc/qbk/release_notes.qbk

```diff
  14 |+ [heading Boost 1.74]
  15 |+ 
  16 |+ [*API Changes]
```

> Username: vinniefalco  
> Created_at: 2020-07-02 17:46:47 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449177290  

There should be a separate heading  
```  
[*Asio Changes]  
  
The Boost.Asio library upon which Beast depends has significant  
changes and improvements which are now used by Beast:  
  
...  
```


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 17:47:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441905820  

📁 doc/qbk/release_notes.qbk

```diff
  30 |+    `net::execution::execute(net::prefer(e, net::execution::blocking.possibly, net::execution::allocator(a)), std::forward<F>(f));`
  31 |+ 
  32 |+  * Asio introduces a new preprocessor macro BOOST_ASIO_NO_TS_EXECUTORS. If this is defined, the deprecated interfaces
```

> Username: vinniefalco  
> Created_at: 2020-07-02 17:47:17 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449177534  

Backticks for `BOOST_ASIO_NO_TS_EXECUTORS`


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 17:47:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441906059  

📁 doc/qbk/release_notes.qbk

```diff
  37 |+ [*Changes Required]
  38 |+ 
  39 |+ * Boost.Beast is tested with both BOOST_ASIO_NO_DEPRECATED set and with BOOST_ASIO_NO_TS_EXECUTORS both set and
```

> Username: vinniefalco  
> Created_at: 2020-07-02 17:47:41 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449177736  

We can just call it Beast here


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-02 17:50:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-441907841  

📁 doc/qbk/release_notes.qbk

```diff
 115 |+ 
 116 |+ * Additional tests have been added to ensure correct integration with C++20
 117 |+   coroutines when avaialable.
```

> Username: vinniefalco  
> Created_at: 2020-07-02 17:50:27 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449179104  

Why is this in the 1.73 notes?


---

## Comment 40

> Username: chriskohlhoff  
> Created_at: 2020-07-02 23:09:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653255321  

The code in `boost/beast/websocket/impl/stream_impl.hpp` only works with the standard executors. A change like the following fixes it:  
  
```cpp  
--- a/include/boost/beast/websocket/impl/stream_impl.hpp  
+++ b/include/boost/beast/websocket/impl/stream_impl.hpp  
@@ -128,8 +128,8 @@ struct stream<NextLayer, deflateSupported>::impl_type  
             boost::empty_init_t{},  
             std::forward<Args>(args)...)  
         , detail::service::impl_type(  
-            net::query(this->boost::empty_value<NextLayer>::get().get_executor(),  
-                net::execution::context))  
+            this->get_context(  
+                this->boost::empty_value<NextLayer>::get().get_executor()))  
         , timer(this->boost::empty_value<NextLayer>::get().get_executor())  
     {  
         timeout_opt.handshake_timeout = none();  
@@ -477,6 +477,22 @@ struct stream<NextLayer, deflateSupported>::impl_type  
     }  
  
 private:  
+    template<class Executor>  
+    static net::execution_context&  
+    get_context(Executor const& ex,  
+        typename std::enable_if< net::execution::is_executor<Executor>::value >::type* = 0)  
+    {  
+        return net::query(ex, net::execution::context);  
+    }  
+  
+    template<class Executor>  
+    static net::execution_context&  
+    get_context(Executor const& ex,  
+        typename std::enable_if< !net::execution::is_executor<Executor>::value >::type* = 0)  
+    {  
+        return ex.context();  
+    }  
+  
     bool  
     is_timer_set() const  
     {  
```  
  
You might consider adding a build with `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT` defined to your test matrix, as it will help catch problems like this. (This define gives users the option of 100% backward compatibility, in case they need it.)

---

## Comment 41

> Username: chriskohlhoff  
> Created_at: 2020-07-03 01:54:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653291362  

> bug in co_spawn:  
  
Now fixed on boostorg/asio develop branch.

---

## Review 42 [Commented]

> Username: chriskohlhoff  
> Created_at: 2020-07-03 13:23:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1999#pullrequestreview-442410348  

📁 include/boost/beast/websocket/impl/stream_impl.hpp

```diff
 131 |-             net::query(this->boost::empty_value<NextLayer>::get().get_executor(),
 132 |-                 net::execution::context))
 131 |+             get_context(this->boost::empty_value<NextLayer>::get().get_executor()))
```

> Username: chriskohlhoff  
> Created_at: 2020-07-03 13:23:15 UTC  
> Updated_at: 2020-07-03 13:34:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#discussion_r449581621  

N.B. In my local changes, I qualified this call with `this->` to ensure ADL wouldn't be used.


---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2020-07-03 13:38:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1999#issuecomment-653553489  

An automated preview of the documentation is available at [http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1999.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
