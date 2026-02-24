# #3053 Fix cancellation slot retrieval in saved_handler::emplace. [Merged]

> Username: biljazovic  
> Created at: 2025-11-05 13:47:11 UTC  
> Updated at: 2025-11-05 18:19:30 UTC  
> Merged at: 2025-11-05 18:19:30 UTC  
> Closed at: 2025-11-05 18:19:30 UTC  
> Url: https://github.com/boostorg/beast/pull/3053  

Bug introduced in a3d00a6d0c551026dc7e763f0297f974d3b062a4

---

## Comment 1

> Username: ashtum  
> Created_at: 2025-11-05 14:03:01 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3491388110  

Thank you. I'm wondering how our CI didn't catch this at all. Did you find it in a sanitizer build?

---

## Comment 2

> Username: biljazovic  
> Created_at: 2025-11-05 14:06:30 UTC  
> Updated_at: 2025-11-05 14:07:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3491406017  

UBSan found it here https://github.com/biljazovic/async-mqtt5/actions/runs/19098896699/job/54565719299  
  
websocket::stream::async_close is in parallel group with timer wait, see here https://github.com/boostorg/mqtt5/blob/develop/include/boost/mqtt5/impl/shutdown_op.hpp and here https://github.com/boostorg/mqtt5/blob/develop/include/boost/mqtt5/websocket.hpp

---

## Comment 3

> Username: ashtum  
> Created_at: 2025-11-05 14:21:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3491487684  

Interesting, Our Calng-18 Ubsan build was cool about it: https://drone.cpp.al/boostorg/beast/1283/36/2  
Probably because most of the handlers in the tests don't have an associated cancellation slot to trigger it.

---

## Comment 4

> Username: biljazovic  
> Created_at: 2025-11-05 14:44:58 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3491619447  

For websocket specifically, from what I can see, saved_handler is used only when there are multiple read or multiple write operations outstanding, e.g. async_write and async_close. In websocket/cancel.cpp test, operations are executed sequentially so it doesn't trigger this.

---

## Comment 5

> Username: ashtum  
> Created_at: 2025-11-05 15:39:43 UTC  
> Updated_at: 2025-11-05 15:53:10 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3491956632  

> For websocket specifically, from what I can see, saved_handler is used only when there are multiple read or multiple write operations outstanding, e.g. async_write and async_close. In websocket/cancel.cpp test, operations are executed sequentially so it doesn't trigger this.  
  
Correct, we have other tests that cover that, but the completion handler used there doesn't have an associated cancellation slot.

---

## Comment 6

> Username: ashtum  
> Created_at: 2025-11-05 17:04:25 UTC  
> Url: https://github.com/boostorg/beast/pull/3053#issuecomment-3492361492  

Looks like there are enough tests to cover that: https://github.com/boostorg/beast/blob/5d6edfabfa9e2532d7a5e4432c05763f12547483/test/beast/core/saved_handler.cpp#L141  
I believe the difference is that in a regular handler, the cancellation slot type remains valid when handler is moved, whereas in asio's `parallel_group_op_handler`, the cancellation slot [is retrieved from the signal on demand](https://github.com/boostorg/asio/blob/f23e04f1ef1e0eae025ecfaa6ddfc6d5f76709f6/include/boost/asio/experimental/impl/parallel_group.hpp#L198-L201), which is what triggered the sanitizer. I should say we were really lucky :)

---
