# #1115 - Recommendations on how to write unit-tests for a beast based server. [Closed]

> Username: trivigy  
> Created at: 2018-05-04 13:43:28 UTC  
> Updated at: 2018-06-04 15:41:56 UTC  
> Closed at: 2018-06-04 15:41:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1115  

Nothing else to add. Just wanted to start a thread for people to share their structure for writing unit-tests for a beast based server.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-04 15:19:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1115#issuecomment-386634427  

Funny you should ask...I am publishing the `test::stream` class used internally in the beast unit tests to a new  experimental include directory:  
https://github.com/vinniefalco/beast/blob/f48b9780448d091ffe22f17783143facdfec82e0/include/boost/beast/experimental/test/stream.hpp#L37

---

## Comment 2

> Username: knejadfard  
> Created at: 2018-05-04 16:00:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1115#issuecomment-386646794  

Nicely timed question and answer! I was wondering the exact same thing yesterday!

---

## Comment 3

> Username: trivigy  
> Created at: 2018-05-04 16:15:44 UTC  
> Updated at: 2018-05-04 16:16:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1115#issuecomment-386650964  

@vinniefalco any example on how to use that when combining [http_client_async](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/async/http_client_async.cpp) and [http_server_async](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/server/async/http_server_async.cpp). Not to be tooooo specific. :rofl:   
  
Honestly I am just looking for some example of how people actually organize this kind of testing in a `Boost.Units` context

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-05 15:33:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1115#issuecomment-386813693  

What do I for Beast is as follows:  
  
1. Decompose higher level abstractions into their individual parts which use stream algorithms  
  
2. Express stream algorithms using templates and the Asio stream concepts **SyncReadStream**, **SyncWriteStream**, **AsyncReadStream**, **AsyncWriteStream**  
  
3. In a test, invoke the stream algorithm using a `test::stream`:  
https://github.com/vinniefalco/beast/blob/f48b9780448d091ffe22f17783143facdfec82e0/include/boost/beast/experimental/test/stream.hpp#L37  
  
4. To exercise all branches in the implementation being tested, instantiate the `test::stream` using a `test::fail_count`:  
https://github.com/vinniefalco/beast/blob/f48b9780448d091ffe22f17783143facdfec82e0/include/boost/beast/experimental/test/fail_count.hpp#L21  
  
5. Exercise the implementation inside a loop where the fail counter is incremented until it reaches some upper limit (indicating that the code being tested is incorrect) or until the operation succeeds. Example:  
https://github.com/vinniefalco/beast/blob/f48b9780448d091ffe22f17783143facdfec82e0/test/beast/http/write.cpp#L354  
  
6. At this point I look at the code coverage report and write individual functions which exercise any parts of the code that were not reached by the looping approach above. I aim for 100% coverage:  
https://codecov.io/gh/vinniefalco/beast/src/v170/include/boost/beast/websocket/impl/close.ipp

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-06-04 15:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1115#issuecomment-394399875  

This issue has been open for a while with no activity, has it been resolved?
