# #286 Add parse event init, node and done. [Merged]

> Username: mzimbres  
> Created at: 2025-07-25 07:49:45 UTC  
> Updated at: 2025-08-10 15:04:26 UTC  
> Merged at: 2025-08-10 15:04:26 UTC  
> Closed at: 2025-08-10 15:04:26 UTC  
> Url: https://github.com/boostorg/redis/pull/286  

This PR implements parse events and is a prerequisite to the `generic_flat_response` from @D0zee.

---

## Review 1 [Commented]

> Username: anarthal  
> Created_at: 2025-07-25 15:16:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/286#pullrequestreview-3055731193  

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  46 |  */
  35 |- class any_adapter {
  47 |+ using any_adapter = std::function<void(parse_event, resp3::node_view const&, system::error_code&)>;
```

> Username: anarthal  
> Created_at: 2025-07-25 14:45:26 UTC  
> Updated_at: 2025-07-25 15:16:41 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231304835  

I don't think this is the right interface for `any_adapter`, because  
  
* There's now a discrepancy between what adapters support (3 functions with different parameters) and what `any_adapter` is (a single function with an enum)  
* Users can't create `any_adapter` objects, since the only practical way is using `make_any_adapter`, which is in detail.  
  
Instead of this, I'd make the `std::function` and the enum an implementation detail, like this:  
  
```cpp  
class any_adapter {  
   enum class parse_event  
   {  
      /// Called before the parser starts processing data  
      init,  
      /// Called for each and every node of RESP3 data  
      node,  
      /// Called when done processing a complete RESP3 message  
      done  
   };  
  
   using fn_type = std::function<void(parse_event, resp3::node_view const*, system::error_code*)>;  
   fn_type impl_;  
  
   template <class Response>  
   static fn_type create_impl(Response& r)  
   {  
      using namespace boost::redis::adapter;  
  
      return [adapter = boost_redis_adapt(  
                 r)](parse_event ev, resp3::node_view const* nd, system::error_code* ec) mutable {  
         switch (ev) {  
            case parse_event::init: adapter.on_init(); break;  
            case parse_event::node: adapter.on_node(*nd, *ec); break;  
            case parse_event::done: adapter.on_done(); break;  
         }  
      };  
   }  
  
public:  
   template <class T, class = std::enable_if_t<!std::is_same_v<T, any_adapter>>>  
   explicit any_adapter(T& resp)  
   : impl_(create_impl(resp))  
   { }  
  
   void on_init() { impl_(parse_event::init, nullptr, nullptr); };  
  
   void on_done() { impl_(parse_event::done, nullptr, nullptr); };  
  
   void on_node(resp3::node_view const& nd, system::error_code& ec)  
   {  
      impl_(parse_event::node, &nd, &ec);  
   };  
};  
```

---

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  94 |+ private:
  95 |+    any_adapter adapter_;
  96 |+    std::size_t expected_responses_ = 0;
```

> Username: anarthal  
> Created_at: 2025-07-25 15:05:44 UTC  
> Updated_at: 2025-07-25 15:16:41 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231363595  

I don't think this belongs here. You already maintain this information in `multiplexer::elem::remaining_responses_`. You can use this variable and go.


📁 include/boost/redis/adapter/detail/response_traits.hpp

```diff
  36 |- 
  37 |-    [[nodiscard]]
  38 |-    auto get_supported_response_size() const noexcept
```

> Username: anarthal  
> Created_at: 2025-07-25 15:08:36 UTC  
> Updated_at: 2025-07-25 15:16:41 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231371922  

It's a shame that we're getting rid of this because I think it helped debugging. Still, I think the implementation wasn't very complete, so I'm good with removing it for now. I'd open an issue to implement this better. Something such that if I type:  
  
  
```cpp  
request req;  
req.push("ping", "abc");  
req.push("ping", "def");  
  
response<std::string> res;  
  
co_await conn.async_exec(req, res);  
```  
  
Gets a proper diagnostic (I think right now it'll get a `std::out_of_range` from `vector::at`).

> Username: mzimbres  
> Created_at: 2025-07-25 19:06:23 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231829974  

Exactly, the library should issue good diagnostics about such errors, for example  
  
> Response object cannot accommodate the responses to all commands in the request.  
  
Allowing `at()` to throw is not ok IMO.

> Username: mzimbres  
> Created_at: 2025-07-25 19:13:55 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231841621  

Offloaded in https://github.com/boostorg/redis/issues/288


📁 test/test_any_adapter.cpp

```diff
  29 |-    BOOST_CHECK_NO_THROW(any_adapter{r3});
  30 |-    BOOST_CHECK_NO_THROW(any_adapter{ignore});
  28 |+    BOOST_CHECK_NO_THROW(make_any_adapter(r1));
```

> Username: anarthal  
> Created_at: 2025-07-25 15:09:12 UTC  
> Updated_at: 2025-07-25 15:16:41 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2231373775  

I think that these should work as they are (without the make_).


---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-07-28 09:38:01 UTC  
> Url: https://github.com/boostorg/redis/pull/286#issuecomment-3126389705  

@anarthal I think all concerns were addressed, please have a look at it again.

---

## Review 3 [Commented]

> Username: anarthal  
> Created_at: 2025-07-28 10:42:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/286#pullrequestreview-3061837950  

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  37 |+    /** @brief Parse events that an adapter must support.
  38 |+     */
  39 |+    enum class parse_event
```

> Username: anarthal  
> Created_at: 2025-07-28 10:33:05 UTC  
> Updated_at: 2025-07-28 10:42:06 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2235785373  

What's the point of making `parse_event`, `impl_type` and the constructor from `impl_type` public and the constructor from the adapter private? `parse_event` and `impl_type` look like private things, while as a user, I want to be able to construct `any_adapter` objects from typed adapters.

> Username: mzimbres  
> Created_at: 2025-07-28 11:07:52 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2235906744  

After the Boost review customization for your own response was to based on providing `boost_redis_adapt()` that would be found via ADL. No `async_exec` overload that took adapters were expose.  
  
Now that adapters are exposed in the async_exec overload it does not make sense to provide `boost_redis_adapt` for a custom response since the user can just construct an `any_adapter` himself by passing an `impl_type`. To enable that we have to make `impl_type` and `parse_event` public types.

> Username: anarthal  
> Created_at: 2025-07-28 11:36:18 UTC  
> Updated_at: 2025-07-28 11:36:19 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2236008556  

Why can't the user just write an adapter like this?  
  
```cpp  
class my_adapter {  
  // impl  
public:  
    void on_init() { /* */ }  
    void on_node(resp3::node_view const&, system::error_code&) { /* */ }  
    void on_finish {}  
};  
  
// Now use it  
conn.async_exec(req, any_adapter(my_adapter(/* whatever */));  
```

> Username: mzimbres  
> Created_at: 2025-07-28 11:44:54 UTC  
> Updated_at: 2025-07-28 11:44:55 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2236040132  

This is supported in this PR: https://github.com/boostorg/redis/blob/2decc7b5120dd6f2f06a0b2ca93c016810e3aed7/include/boost/redis/adapter/any_adapter.hpp#L58-L71

> Username: anarthal  
> Created_at: 2025-07-28 12:04:09 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2236110178  

It says "it is only used internally, so not documented"

---

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  57 |+    // No need to document this as it is only used internally.
  58 |+    template <class Adapter>
  59 |+    any_adapter(Adapter adapter)
```

> Username: anarthal  
> Created_at: 2025-07-28 10:34:28 UTC  
> Updated_at: 2025-07-28 10:42:06 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2235790819  

Making this an `Adapter` (vs a `Response&`) is a breaking API change. To be honest, it makes sense for `any_adapter` to be constructible from an `Adapter` rather than a `Response&`, though. It is also true that all uses I've found end up calling `boost_redis_adapt`, so this feels like making syntax a little bit more convoluted. Is there any chance to make `any_adapter(response)` keep working?

> Username: mzimbres  
> Created_at: 2025-07-28 11:46:55 UTC  
> Updated_at: 2025-07-28 11:46:56 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2236047861  

I thought this was a small enough breaking change that wouldn't hurt anyone.

> Username: anarthal  
> Created_at: 2025-07-28 12:04:45 UTC  
> Url: https://github.com/boostorg/redis/pull/286#discussion_r2236112748  

It will break at least the user who originally requested `any_adapter`


---

## Comment 4

> Username: anarthal  
> Created_at: 2025-07-28 10:42:43 UTC  
> Url: https://github.com/boostorg/redis/pull/286#issuecomment-3126632434  

@mzimbres see my comments. I still have concerns about `any_adapter`'s current interface.

---

## Comment 5

> Username: mzimbres  
> Created_at: 2025-08-02 11:39:41 UTC  
> Url: https://github.com/boostorg/redis/pull/286#issuecomment-3146453041  

@anarthal The response constructor in `any_adapter` has been reestablished. Please have a look.

---

## Review 6 [Approved]

> Username: anarthal  
> Created_at: 2025-08-10 13:14:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/redis/pull/286#pullrequestreview-3103621520  

This looks much better. If I have the time, I'd like to add a constructor to any_adapter from an `Adapter&&`, but that can be added later. Feel free to merge the PR.

---
