# #2728 Replace BOOST_ASIO_INITFN_RESULT_TYPE with BOOST_ASIO_INITFN_AUTO_RESULT_TYPE [Merged]

> Username: ashtum  
> Created at: 2023-08-21 11:51:57 UTC  
> Updated at: 2023-09-05 05:24:09 UTC  
> Merged at: 2023-09-05 05:24:09 UTC  
> Closed at: 2023-09-05 05:24:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2728  

Fixes #2727.

---

## Review 1 [Approved]

> Username: klemens-morgenstern  
> Created_at: 2023-08-21 13:09:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/2728#pullrequestreview-1586953294  

LGTM. Check that it works with C++11 though, maybe we need the PREFIX & _SUFFIX version.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-21 17:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1686761612  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-08-22 06:17:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1687522369  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-08-22 07:05:12 UTC  
> Updated_at: 2023-09-04 06:28:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1687595157  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2728](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9911a07) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/35d533299c129d622af774f96b820e8f18df068f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (35d5332) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2728/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2728      +/-   ##  
===========================================  
+ Coverage    92.94%   92.97%   +0.02%       
===========================================  
  Files          177      177                
  Lines        13658    13658                
===========================================  
+ Hits         12694    12698       +4       
+ Misses         964      960       -4       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/beast/\_experimental/test/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaHBw) | `98.68% <ø> (ø)` | |  
| [include/boost/beast/\_experimental/test/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `75.00% <ø> (ø)` | |  
| [include/boost/beast/core/basic\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `80.39% <ø> (ø)` | |  
| [include/boost/beast/core/detect\_ssl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `92.42% <ø> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `86.11% <ø> (ø)` | |  
| [include/boost/beast/ssl/ssl\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9zc2wvc3NsX3N0cmVhbS5ocHA=) | `0.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2728/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [35d5332...9911a07](https://app.codecov.io/gh/boostorg/beast/pull/2728?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-08-22 08:37:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1687733803  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-08-22 10:57:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1687963738  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-08-22 11:07:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1687977069  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-08-22 11:35:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1688015695  

![pullrequest](https://2728.beast.tracing.cppalliance.org/pullrequest-5c1b2647.png)  
Timeline tracing charts: [https://2728.beast.tracing.cppalliance.org/index.html](https://2728.beast.tracing.cppalliance.org/index.html)

---

## Review 9 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2023-08-23 05:21:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2728#pullrequestreview-1590772392  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
  41 | #else // defined(BOOST_ASIO_NO_TS_EXECUTORS)
  47 |-         net::executor_work_guard<ex2_type> wg2_;
  42 |+         net::executor_work_guard<net::any_io_executor> wg2_;
```

> Username: klemens-morgenstern  
> Created_at: 2023-08-23 05:20:55 UTC  
> Updated_at: 2023-08-23 05:21:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#discussion_r1302494481  

What is this change for?

> Username: ashtum  
> Created_at: 2023-08-23 05:44:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#discussion_r1302508065  

There was a compile time error in this line:  
https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/_experimental/test/impl/stream.hpp#L67  
  
The error occurres when the associated executor of the handler is different from the wg2 executor. This only happens during the instantiation of the [is_async_read_stream](https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/core/stream_traits.hpp#L337) trait. I believe this is due to the fact that detail::ReadHandler has no associated executor and uses s->exec in that line.  
  
You can find the exact error here:  
https://github.com/boostorg/beast/actions/runs/5925847678/job/16066139656#step:9:1423  
```BASH  
clang-linux.compile.c++ ../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden/accept.o  
In file included from test/beast/websocket/accept.cpp:13:  
In file included from ../../boost/beast/_experimental/test/stream.hpp:617:  
../../boost/beast/_experimental/test/impl/stream.hpp:67:15: error: no matching constructor for initialization of 'net::executor_work_guard<ex2_type>' (aka 'executor_work_guard<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>')  
            , wg2_(net::get_associated_executor(  
              ^    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../boost/beast/_experimental/test/impl/stream.hpp:144:11: note: in instantiation of function template specialization 'boost::beast::test::basic_stream<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::read_op<boost::beast::detail::StreamHandler, boost::beast::detail::BufferSequence<boost::asio::mutable_buffer>>::lambda::lambda<boost::beast::detail::StreamHandler>' requested here  
        : fn_(std::forward<Handler_>(h), s, b)  
          ^  
../../boost/beast/_experimental/test/impl/stream.hpp:192:17: note: in instantiation of function template specialization 'boost::beast::test::basic_stream<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::read_op<boost::beast::detail::StreamHandler, boost::beast::detail::BufferSequence<boost::asio::mutable_buffer>>::read_op<boost::beast::detail::StreamHandler>' requested here  
            new read_op<  
                ^  
../../boost/asio/async_result.hpp:482:5: note: in instantiation of function template specialization 'boost::beast::test::basic_stream<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::run_read_op::operator()<boost::beast::detail::StreamHandler, boost::beast::detail::BufferSequence<boost::asio::mutable_buffer>>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
../../boost/asio/detail/config.hpp:135:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
../../boost/asio/async_result.hpp:896:21: note: in instantiation of function template specialization 'boost::asio::detail::completion_handler_async_result<boost::beast::detail::StreamHandler, void (boost::system::error_code, unsigned long)>::initiate<boost::beast::test::basic_stream<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::run_read_op, boost::beast::detail::StreamHandler, boost::shared_ptr<boost::beast::test::detail::stream_state> &, const boost::beast::detail::BufferSequence<boost::asio::mutable_buffer> &>' requested here  
    Signatures...>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                    ^  
../../boost/beast/_experimental/test/impl/stream.hpp:343:17: note: in instantiation of function template specialization 'boost::asio::async_initiate<boost::beast::detail::StreamHandler, void (boost::system::error_code, unsigned long), boost::beast::test::basic_stream<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::run_read_op, boost::shared_ptr<boost::beast::test::detail::stream_state> &, const boost::beast::detail::BufferSequence<boost::asio::mutable_buffer> &>' requested here  
    return net::async_initiate<  
                ^  
../../boost/asio/executor_work_guard.hpp:97:12: note: candidate constructor not viable: no known conversion from 'const boost::asio::detail::associated_executor_impl<boost::beast::detail::StreamHandler, boost::asio::any_io_executor>::type' (aka 'const boost::asio::any_io_executor') to 'const boost::asio::executor_work_guard<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>::executor_type' (aka 'const boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>') for 1st argument  
  explicit executor_work_guard(const executor_type& e) BOOST_ASIO_NOEXCEPT  
           ^  
../../boost/asio/executor_work_guard.hpp:104:3: note: candidate constructor not viable: no known conversion from 'const boost::asio::detail::associated_executor_impl<boost::beast::detail::StreamHandler, boost::asio::any_io_executor>::type' (aka 'const boost::asio::any_io_executor') to 'const boost::asio::executor_work_guard<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>' for 1st argument  
  executor_work_guard(const executor_work_guard& other) BOOST_ASIO_NOEXCEPT  
  ^  
../../boost/asio/executor_work_guard.hpp:113:3: note: candidate constructor not viable: no known conversion from 'const boost::asio::detail::associated_executor_impl<boost::beast::detail::StreamHandler, boost::asio::any_io_executor>::type' (aka 'const boost::asio::any_io_executor') to 'boost::asio::executor_work_guard<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0>>' for 1st argument  
  executor_work_guard(executor_work_guard&& other) BOOST_ASIO_NOEXCEPT  
  ^  
1 error generated.  
  
    "clang++-12"   -std=c++14 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_BEAST_TESTS -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -D_GNU_SOURCE=1 -D_XOPEN_SOURCE=600   -I"." -I"../.." -I"test/extern" -I"test/extras/include"  -c -o "../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden/accept.o" "test/beast/websocket/accept.cpp"  
  
...failed clang-linux.compile.c++ ../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden/accept.o...  
...skipped <p../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden>accept for lack of <p../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden>accept.o...  
...skipped <p../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden>accept.run for lack of <p../../bin.v2/libs/beast/test/beast/websocket/accept.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden>accept...  
clang-linux.link ../../bin.v2/libs/beast/test/beast/websocket/any_completion_handler.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden/any_completion_handler  
testing.capture-output ../../bin.v2/libs/beast/test/beast/websocket/any_completion_handler.test/clang-linux-12/debug/cxxstd-14-iso/threading-multi/visibility-hidden/any_completion_handler.run  
  
```


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-09-02 08:22:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1703762817  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 11 [Commented]

> Username: ashtum  
> Created_at: 2023-09-02 08:29:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2728#pullrequestreview-1608025783  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
  47 |-         net::executor_work_guard<ex2_type> wg2_;
  42 |+         net::executor_work_guard<
  43 |+             net::associated_executor_t<Handler, net::any_io_executor>> wg2_;
```

> Username: ashtum  
> Created_at: 2023-09-02 08:29:21 UTC  
> Updated_at: 2023-09-02 08:40:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#discussion_r1313779333  

@klemens-morgenstern Here we use any_io_executor when there is no associated executor because it will be initialized with s->exec (when there is no associated executor) in the following line: https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/_experimental/test/impl/stream.hpp#L68  
  
`stream_state::exec` type is any_io_executor:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/test/detail/stream_state.hpp#L88  
  
This was causing compile time error in instantiation of `is_async_read_stream` type trait, because the handler has no associated executor thus it tries to initialize `wg2_` with an instance of `s->exec`. which wg2_ has an executor type of io_context::basic_executor_type.

> Username: klemens-morgenstern  
> Created_at: 2023-09-02 10:09:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#discussion_r1313798411  

I don't think this is correct. If you don't haven associated one shouldn't you default to `ex1_type`?

> Username: ashtum  
> Created_at: 2023-09-02 10:17:45 UTC  
> Updated_at: 2023-09-02 10:19:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#discussion_r1313799407  

`ex1_type` is `basic_stream<Executor>::executor_type` but we are initialization the `wg2_` with `s->exec` which is `any_io_executor`.  
Similar to what is done for `wg1_`:  
https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/_experimental/test/impl/stream.hpp#L133  
https://github.com/boostorg/beast/blob/35d533299c129d622af774f96b820e8f18df068f/include/boost/beast/_experimental/test/impl/stream.hpp#L146


---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-09-04 05:17:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2728#issuecomment-1704625242  

An automated preview of the documentation is available at [https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2728.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
