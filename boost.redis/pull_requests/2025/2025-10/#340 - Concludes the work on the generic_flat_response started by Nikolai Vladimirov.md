# #340 Concludes the work on the generic_flat_response started by Nikolai Vladimirov [Merged]

> Username: mzimbres  
> Created at: 2025-10-25 12:59:13 UTC  
> Updated at: 2025-11-18 13:10:40 UTC  
> Merged at: 2025-11-18 13:10:23 UTC  
> Closed at: 2025-11-18 13:10:24 UTC  
> Url: https://github.com/boostorg/redis/pull/340  

@anarthal, let us start the review for `generic_flat_response`. I need the `async_receive2` so I committed my own implementation and will rebase on top of yours if you find time to implement it.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-26 12:41:28 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3448484061  

Do we have any numbers comparing the performance of this approach (converting to string_view immediately after parsing) vs. the custom iterator approach (converting to string_view on iteration)?  
  
I will have a look at this tomorrow

---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-10-26 20:56:00 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3448918419  

> Do we have any numbers comparing the performance of this approach (converting to string_view immediately after parsing) vs. the custom iterator approach (converting to string_view on iteration)?  
  
IMO this is not worth the effort. Both cases must contruct construct the `string_view` but the custom iterator approach must copy more data i.e. `node&` vs `node` so it can only be slower.  
  
Performance however is not my main concern because it is probably in sub microsend difference, which is hard to measure.  In essence I regard the `T at(std::size_t);` api to be inferior to `T const& at(std::size_t)` since the latter requires reconstructing the node on each call (imagine needing to call it into a loop). Changing the container is probably never going to be needed, but I at least the current API does not make it impossible.  
   
> I will have a look at this tomorrow  
  
Thanks. Please, take into account I hurried a bit to ovoid delaying discussion another week, specially on what we should do with `async_receive2`.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-10-27 10:55:45 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3450685803  

The only thing I'm not convinced of is exposing this new `offset_string` type. It's more API surface, and it's not "A `std::string`-like type.", which is what `basic_node<String>` requires from `String`.  
  
I'm writing some benchmarks and thinking to see if there is any chance of using `basic_node<std::string_view>` with the current approach.  
  
Testing and docs could use a couple of improvements, but I don't think there's a need to delay merging this because of these two - I can make some improvements later.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-10-27 13:43:31 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3451362531  

Concluded the benchmarks. TL;DR: let's use the separate `vector<offset_and_size>` for now.  
  
Benchmark results (explanation follows):  
  
|                                      |clang-20|      clang-20 libc++    |gcc-13|  
|--------------------------------------|-------|--------------------------|-------|  
|BM_push_no_reuse<original_impl>       |8331 ns|      9274 ns             |10633 ns|  
|BM_push_no_reuse<custom_buffer>       |5451 ns|      7011 ns             |5066 ns|  
|BM_push_no_reuse<offset_vector>       |8781 ns|     10816 ns             |8551 ns|  
|BM_push_reuse<original_impl>          |4999 ns|      6533 ns             |7731 ns|  
|BM_push_reuse<custom_buffer>          |2987 ns|      5453 ns             |2767 ns|  
|BM_push_reuse<offset_vector>          |5040 ns|      7461 ns             |4928 ns|  
|BM_big_no_reuse<original_impl>        |2668 ns|      2902 ns             |3126 ns|  
|BM_big_no_reuse<custom_buffer>        |1927 ns|      2342 ns             |1893 ns|  
|BM_big_no_reuse<offset_vector>        |2835 ns|      3332 ns             |2761 ns|  
|BM_big_reuse<original_impl>           |1308 ns|      1837 ns             |2039 ns|  
|BM_big_reuse<custom_buffer>           | 838 ns|      1417 ns             |748 ns|  
|BM_big_reuse<offset_vector>           |1335 ns|      1900 ns             |1425 ns|  
  
Full code: https://gist.github.com/anarthal/73a8e081dc9de3f390e6d0ec1d5e2b34  
  
Implementations:  
  
* `generic_flat_response_value` (matching `original_impl` in the table above): what is in the PR.  
* `generic_flat_response_value_v2`: never used, it's a proof of concept but may be UB.  
* `generic_flat_response_value_v3` (matching `custom_buffer` in the table above): we store only string views. Every time the buffer reallocates, we use the old data pointer to compute the offset and update the string view. Doesn't use any adapter hooks.  
* `generic_flat_response_value_v4` (matching `offset_vector` in the table above): we use regular node_view nodes, and a separate vector for sizes/offsets, as you proposed.  
  
The benchmarks:  
  
* Push: simulates receiving 100 pushes.  
* Big: simulates receiving a response with 100 nodes.  
* Reuse: the same response object is reused over and over.  
* No reuse: a new response object is created every time.  
  
The custom buffer approach seems consistently faster in all benchmarks. It's however much more implementation work, so I'd discourage from taking this way at least for now. The other two options seem similar. offset_vector would allow us to get rid of offset_string, so I suggest taking this path.

---

## Review 5 [Commented]

> Username: anarthal  
> Created_at: 2025-10-27 14:15:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/340#pullrequestreview-3383602901  

📁 test/CMakeLists.txt

```diff
   6 |   # C4459: name hides outer scope variable is issued by Asio
   7 |-   target_compile_options(boost_redis_project_options INTERFACE /bigobj /W4 /WX /wd4459)
   7 |+   target_compile_options(boost_redis_project_options INTERFACE /bigobj /W4 /WX /wd4459 /w34996)
```

> Username: anarthal  
> Created_at: 2025-10-27 13:47:58 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465751288  

We shouldn't need this or -Wno-deprecated-declarations. We already define `BOOST_ALLOW_DEPRECATED` in the tests, and examples shouldn't use deprecated features.


📁 include/boost/redis/generic_flat_response_value.hpp

```diff
  16 |+ 
  17 |+ #include <string>
  18 |+ #include <tuple>
```

> Username: anarthal  
> Created_at: 2025-10-27 13:48:58 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465754455  

Unused include

---

📁 include/boost/redis/generic_flat_response_value.hpp

```diff
  71 |+ 
  72 |+    // Notify the object that all nodes were pushed.
  73 |+    void notify_done();
```

> Username: anarthal  
> Created_at: 2025-10-27 14:01:24 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465800765  

These should be made private with a detail accessor. But this is not urgent, I can do it afterwards.


📁 include/boost/redis/resp3/flat_tree.hpp

```diff
  68 |+     */
  69 |+    auto get_reallocs() const noexcept
  70 |+       { return reallocs_; }
```

> Username: anarthal  
> Created_at: 2025-10-27 13:51:51 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465764095  

Better use explicit return type here


📁 include/boost/redis/connection.hpp

```diff
1094 |    /// @copydoc basic_connection::async_receive
1095 |    template <class CompletionToken = asio::deferred_t>
1096 |+    BOOST_DEPRECATED("Please use async_receive2 instead.")
```

> Username: anarthal  
> Created_at: 2025-10-27 13:57:51 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465785412  

You probably need `-Wno-deprecated-declarations` and friends because `impl_.async_receive` and `impl_.receive` are deprecated. This means that the user is going to see these warnings when they build `src.hpp`, and we don't want this.  
  
The way I usually solve this is by moving the function from `basic_connection` to `connection_impl`, then calling this second function from both `basic_connection` and `connection`.

> Username: mzimbres  
> Created_at: 2025-11-01 17:41:41 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2483812257  

The problem is that `connection` calls on `basic_connection` functions which call on `connection_impl` functions. So in connection we have  
```cpp  
std::size_t receive(system::error_code& ec) { return impl_.receive(ec); }  
```  
and `impl_.receive` is deprecated in `basic_connection`. I will check if I can use `impl.impl.receive()` or something.

> Username: anarthal  
> Created_at: 2025-11-01 18:00:17 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2483818419  

IIRC you can because they're friend classes (I added that because of a similar problem)


📁 test/test_conn_push.cpp

```diff
  99 |    net::io_context ioc;
 100 |    auto conn = std::make_shared<connection>(ioc);
```

> Username: anarthal  
> Created_at: 2025-10-27 14:03:24 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465807961  

We should retain at least one test for the deprecated `async_receive` and `receive` so we make sure we don't break them by accident. I suggest duplicating this test to get a minimum of coverage.


📁 include/boost/redis/response.hpp

```diff
  78 |-  * @param r The response to modify.
  79 |-  */
  87 |+ /// @copydoc consume_one
```

> Username: anarthal  
> Created_at: 2025-10-27 14:13:51 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465851410  

There seems to be a MrDocs bug with copydoc and this seems to fail. It doesn't support Doxygen's overload resolution either (https://github.com/cppalliance/mrdocs/issues/842). Since this is deprecated, I suggest keeping the old docs stating that the function is deprecated and go.

---

📁 include/boost/redis/response.hpp

```diff
  41 |+  *  memory reuse.
  42 |+  */
  43 |+ using generic_flat_response = adapter::result<generic_flat_response_value>;
```

> Username: anarthal  
> Created_at: 2025-10-27 14:14:51 UTC  
> Updated_at: 2025-10-27 14:15:18 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465856338  

New classes should be added to the reference page. If you'd like, please open an issue and I'll add it after you merge it.


---

## Review 6 [Commented]

> Username: anarthal  
> Created_at: 2025-10-27 14:32:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/340#pullrequestreview-3383832285  

📁 include/boost/redis/resp3/flat_tree.hpp

```diff
  75 |+    /// Returns the number of complete RESP3 messages contained in this object.
  76 |+    std::size_t get_total_msgs() const noexcept
  77 |+       { return total_msgs_; }
```

> Username: anarthal  
> Created_at: 2025-10-27 14:32:10 UTC  
> Updated_at: 2025-10-27 14:32:14 UTC  
> Url: https://github.com/boostorg/redis/pull/340#discussion_r2465918741  

I completely forgot - but this class copy semantics are wrong. You will get string_views pointing to the old class. You will need to implement a custom copy constructor and copy assignment. You might also mark these operations as deleted for now and implement them later on.


---

## Comment 7

> Username: mzimbres  
> Created_at: 2025-11-16 21:05:36 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3539353728  

@D0zee The PR is finally ready for merging. I have kept all your original commits so that you appear as contributor. You are also listed in the copyright notice in the relevant files, please check if everything is fine. I will merge it this week. Thanks.  
  
@anarthal You can also have a final look if you want. I have added a new typedef  
```cpp  
using tree = std::vector<node>;  
```  
Which we will have to use in you sentinel PR.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-11-16 21:09:46 UTC  
> Updated_at: 2025-11-16 21:10:25 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3539357895  

Not a big fan of the aliases - I think `vector<node>` communicates intent better. I can have a look at it tomorrow if you think it adds value, or you can merge it already otherwise.

---

## Comment 9

> Username: mzimbres  
> Created_at: 2025-11-18 11:04:53 UTC  
> Url: https://github.com/boostorg/redis/pull/340#issuecomment-3546980319  

> Not a big fan of the aliases - I think vector<node> communicates intent better.  
  
For some background. Currently we have the following RESP3 types for a sequence of nodes  
  
  - `std::vector<node>`  
  - `flat_tree`  
  - `std::vector<node_view>`  
  
IMO these namings do not convey in a user friendly way that these types provide the same data layout. Therefore I aliased 1. and 3. to `tree` and `view_tree` ending up with  
  
  - `tree`  
  - `flat_tree`  
  - `view_tree`  
  
The generic responses can now be defined more intuitively as  
  
```cpp  
using generic_response = result<tree>;  
using generic_flat_response = result<flat_tree>;  
```

---
