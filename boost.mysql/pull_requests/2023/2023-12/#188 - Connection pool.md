# #188 Connection pool [Closed]

> Username: anarthal  
> Created at: 2023-12-01 11:39:54 UTC  
> Updated at: 2023-12-26 16:05:04 UTC  
> Closed at: 2023-12-26 16:05:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/188  

Docs: https://anarthal.github.io/connection-pool/libs/mysql/doc/html/index.html  
  
Please read the section on type-eased connections with `any_connection` and connection pools with `connection_pool`.

---

## Review 1 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:13:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mysql/pull/188#pullrequestreview-1762387808  

I didn't get to read all of (didn't look at tests for example), but it looks like a solid start.  
  
I got two main thoughts  
  
1. It can probably be simplified, with among others, using asio::generic::stream_protocol  
2. Some composed ops look a bit off and would need careful reevaluation regarding completion and executors.

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:20:32 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413863823  

don't iddle the connection.


📁 doc/qbk/20_any_connection.qbk

```diff
  22 |+ * Is easier to connect. For example, when using TCP, connection establishment methods will
  23 |+   handle hostname resolution for you. This must be handled manually with `connection`.
  24 |+ * It can always be reconnected after closing it or after encountering an error.
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:21:15 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413864662  

```diff  
-* It can always be reconnected after closing it or after encountering an error.  
+* Can always be reconnected after closing it or after encountering an error.  
```


📁 doc/qbk/21_connection_pool.qbk

```diff
 144 |+ 
 145 |+ * When a connection is created, it goes into the `pending_connect` state.
 146 |+ * Connection establishment is attempted. If it succeeds, the connection becomes `iddle`.
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:22:41 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413866205  

I like that `iddle` is consistently used, but I am pretty sure it should be `idle`, no?

---

📁 doc/qbk/21_connection_pool.qbk

```diff
 182 |+   will be serialized.
 183 |+ * [refmem pool_executor_params connection_executor] is used to construct connections.
 184 |+   By default, this won't be wrapped in any strand, and inividual connections will not be thread-safe.
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:24:24 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413868100  

That's a fair decision. I decided to make requests::pool thread_safe, bc I have a gut feeling about how it will be used. probably different here.

> Username: anarthal  
> Created_at: 2023-12-04 18:59:46 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414353266  

Well, I started making it thread-safe. Then I created the `pool_executor_params` and thought it made sense not doing it, but providing a super easy way to. You can write  
```  
connection_pool pool (pool_executor_params::thread_safe(ex), params);  
```  
And be sure everything will work as expected.

---

📁 doc/qbk/21_connection_pool.qbk

```diff
 203 |+ * When managing hundrends of connections, it's much more efficient to use
 204 |+   async functions and a limited number of threads, than a thread per connection.
 205 |+ * Sync functions don't allow setting timeouts.
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:25:22 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413869196  

For the perf min-maxers you should explain why it ain't templated.

---

📁 doc/qbk/21_connection_pool.qbk

```diff
 151 |+ * If a connection is returned by [refmem pooled_connection return_without_reset],
 152 |+   it becomes `iddle` again.
 153 |+ * If a connection is returned by [reflink pooled_connection]'s destructor, it becomes
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:11:25 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413929011  

This is what I did originally did in requests and I ended up making the returning explicit. But that might be different for mysql.

> Username: anarthal  
> Created_at: 2023-12-04 19:03:40 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414356894  

What was the rationale behind that decision? Was returning a connection async?  
Making it explicit looks like a loophole for resource leaks.

> Username: klemens-morgenstern  
> Created_at: 2023-12-05 22:35:03 UTC  
> Updated_at: 2023-12-05 22:35:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1416356624  

Because you may want to steal a connection (upgrade it to a websocket) and doing implicit things is dtors just seems off. The main difference to mysql however is that you'll use the pooled connection through an algorithm like `http::async_get` about 97% of the time, so users don't need to explicitly return the thing either.


📁 example/any_connection.cpp

```diff
 117 |+             boost::mysql::results result;
 118 |+             conn.async_execute(stmt.bind(company_id), result, diag, yield[ec]);
 119 |+             boost::mysql::throw_on_error(ec, diag);
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:26:20 UTC  
> Updated_at: 2023-12-04 14:13:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413870337  

...why not just yield without `[ec]` ?

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:38:53 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413885038  

Nevermind got it, it's `diag`. I would consider (for a future release) adding a `mysql_throw_with_diagnostics` or sth, like this:  
  
```cpp  
            conn.async_execute(stmt.bind(company_id), result, diag, mysql::throw_with_diag(yield, diag));  
```  
  
or possibly an overload that has a `void(std::exception_ptr)`  signature. THis stuff just sticks out for me.

> Username: anarthal  
> Created_at: 2023-12-04 19:04:35 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414357724  

Yeah, not ideal. Maybe for the future.

---

📁 example/any_connection.cpp

```diff
 130 |+         },
 131 |+         // If any exception is thrown in the coroutine body, rethrow it.
 132 |+         [](std::exception_ptr ptr) {
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:26:56 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413871016  

For this example i'd go for use_future with a .get() at the end.

> Username: anarthal  
> Created_at: 2023-12-20 16:44:18 UTC  
> Updated_at: 2023-12-20 16:44:19 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1432950578  

I think they achieve the same with similar readability.


📁 example/connection_pool/main.cpp

```diff
 140 |+ #include <iostream>
 141 |+ 
 142 |+ int main() { std::cout << "Sorry, your compiler doesn't support C++14\n"; }
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:27:42 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413871881  

```diff  
-int main() { std::cout << "Sorry, your compiler doesn't support C++14\n"; }  
+int main() { std::cout << "Sorry, your compiler doesn't support C++14\n"; return EXIT_FAILURE; }  
```

> Username: anarthal  
> Created_at: 2023-12-04 19:05:56 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414358965  

Then you have one CI failure per C++11 build :)


📁 include/boost/mysql/any_address.hpp

```diff
 223 |+ class any_address
 224 |+ {
 225 |+ #ifndef BOOST_MYSQL_DOXYGEN
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:32:34 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413877686  

Isn't that essentially asio::generic::stream_protocol::endpoint`?

> Username: anarthal  
> Created_at: 2023-12-04 19:17:07 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414369213  

It is for UNIX (except that in this class UNIX is not ifdef-ed out conditionally), but not for TCP (this stores hostnames instead of IPs).

> Username: anarthal  
> Created_at: 2023-12-04 19:17:31 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414369562  

There will be at some point where we will have Windows named pipes, too.


📁 include/boost/mysql/any_connection.hpp

```diff
 305 |+      */
 306 |+     template <BOOST_ASIO_COMPLETION_TOKEN_FOR(void(error_code)) CompletionToken>
 307 |+     auto async_connect(const connect_params* params, diagnostics& diag, CompletionToken&& token)
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:36:26 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413882152  

We talked about this an I get out point. But in this situation, why not have this overload be `const &` and the one above be `&&` ?   
I feel like these days (mostly) only users who know lifetime issues will be using non-coro concepts.

> Username: anarthal  
> Created_at: 2023-12-20 16:46:04 UTC  
> Updated_at: 2023-12-20 16:46:05 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1432952538  

For the record: the `const connect_params&` overload allocates a `std::unique_ptr<char[]>` to hold everything it needs - so having it `connect_params&&` doesn't add a lot. I think I prefer it the way it is - more explicit.


📁 include/boost/mysql/detail/connection_pool/connection_node.hpp

```diff
 187 |+                 {
 188 |+                     // Try to connect
 189 |+                     BOOST_ASIO_CORO_YIELD return next_connection_action::connect;
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:42:30 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413889548  

This looks weird? Why don't you assign `status_` and `return coro_.is_completed() ? invalid : status_` ?

---

📁 include/boost/mysql/detail/connection_pool/connection_node.hpp

```diff
 320 |+                     {
 321 |+                         node_.shared_st_->wait_gp.on_task_finish();
 322 |+                         self.complete(error_code());
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:44:28 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413891946  

the recursion is on purpose?

> Username: anarthal  
> Created_at: 2023-12-04 19:07:20 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414360205  

I actually want to rewrite the `wait_group` thing. It was inspired in Cobalt, btw.


📁 include/boost/mysql/detail/connection_pool/connection_pool_impl.hpp

```diff
  85 |+         void operator()(Self& self, error_code ec = {})
  86 |+         {
  87 |+             // TODO: per-operation cancellation here doesn't do the right thing
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:47:24 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413895823  

That means you need to block it, if you can't support it. Perfectly fine, most things (like coros) will handle it later if you don't.

> Username: anarthal  
> Created_at: 2023-12-04 19:10:03 UTC  
> Updated_at: 2023-12-04 19:10:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414362678  

Well that's something I wanted to ask you about. The right thing would actually be calling `cancel_chan_.try_send(error_code());`. So two questions:  
1) What's the right way to tell Asio to do this?  
2) Wouldn't per-operation cancel here bypass any strand protection?

> Username: anarthal  
> Created_at: 2023-12-20 16:46:39 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1432953121  

Descoped to #197

---

📁 include/boost/mysql/detail/connection_pool/connection_pool_impl.hpp

```diff
 173 |+                 if (obj_->state_ != state_t::running)
 174 |+                 {
 175 |+                     do_complete(self, client_errc::cancelled, pooled_connection());
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:48:22 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413897297  

Shouldn't do_complete dispatch to the handler first?

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:53:14 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413903340  

It's a bit unclear to me if this is safe:  
  
```cpp  
cobalt::promise<void> pro()  
{  
   asio::thread_pool tp;  
   mysql::connection_pool p{asio::make_strand(tp)};  
  
   co_await p.async_get_connection(cobalt::use_op);  
}  
```

> Username: anarthal  
> Created_at: 2023-12-04 19:12:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414364716  

This is likely a bug in the case there are no connections. I need to write unit testing for this with my tracker executor to pick these.  
  
What do you mean by "safe" here? In terms of threading, yes, this should be safe. You could be calling `async_run` and `async_get_connection` from any other thread concurrently.  
  
There's a test under `test/thread_safety/` that I think covers this.

---

📁 include/boost/mysql/detail/connection_pool/connection_pool_impl.hpp

```diff
 275 |+     {
 276 |+         // This is a concurrent channel, no need to dispatch to the strand
 277 |+         cancel_chan_.try_send(error_code());
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:49:01 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413898172  

That looks like there should be an error, probably `asio::error::operation_aborted`.

> Username: anarthal  
> Created_at: 2023-12-04 19:13:08 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414365653  

Well, the value sent through the channel is never exposed to the user. It's just a thread-safe signal to make `async_run` stop.


📁 include/boost/mysql/detail/connection_pool/iddle_connection_list.hpp

```diff
  28 |+ {
  29 |+     intrusive::list<connection_node, intrusive::base_hook<hook_type>> list_;
  30 |+     asio::experimental::channel<void(error_code)> chan_;  // TODO: could we make these void()?
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:54:18 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413905115  

You can't.

> Username: anarthal  
> Created_at: 2023-12-04 19:13:26 UTC  
> Updated_at: 2023-12-04 19:13:27 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1414365929  

Removed comment.


📁 include/boost/mysql/detail/connection_pool/run_with_timeout.hpp

```diff
  52 |+         .async_wait(asio::experimental::wait_for_one(), asio::deferred(adapter))(std::forward<Handler>(handler
  53 |+         ));
  54 |+ }
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:56:55 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413909601  

I understand waht you're doing here, but I feel like custom tokens are better for timeouts. Did you see my post towards that on cppal?   
  
https://cppalliance.org/asio/2023/01/02/Asio201Timeouts.html


📁 include/boost/mysql/detail/connection_pool/task_joiner.hpp

```diff
  32 |+     {
  33 |+         if (--running_tasks_ == 0u)
  34 |+             finished_chan_.try_send(error_code());  // If this happens to fail, terminate() is the best option
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 13:57:45 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413911431  

That's the kind of thing you can also solve with an timer, by using `cancel` as the signal.


📁 include/boost/mysql/impl/internal/ssl_context_with_default.hpp

```diff
  18 |+ class ssl_context_with_default
  19 |+ {
  20 |+     variant2::variant<asio::ssl::context*, asio::ssl::context> impl_;
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:01:45 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413916595  

just fyi: the way I solved this in requests is to put the ssl::context into an `asio::service`.

> Username: anarthal  
> Created_at: 2023-12-20 16:47:17 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1432953838  

Solved with a singleton object.


📁 include/boost/mysql/impl/internal/variant_stream.hpp

```diff
 247 |+         asio::ip::tcp::resolver resolv;
 248 |+ 
 249 |+         socket_and_resolver(asio::any_io_executor ex) : sock(ex), resolv(std::move(ex)) {}
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:02:59 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413918176  

I'd just use `asio::generic::stream_socket here.

> Username: anarthal  
> Created_at: 2023-12-20 16:47:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1432954484  

`stream_socket` is unfortunately incompatible with `asio::connect(const EndpointSequence&)`, so I've left it as is.

---

📁 include/boost/mysql/impl/internal/variant_stream.hpp

```diff
 306 |+             {
 307 |+                 // Setup stream
 308 |+                 stored_ec_ = this_obj_.setup_stream();
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:04:02 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413919669  

why not just use `ec` here?

---

📁 include/boost/mysql/impl/internal/variant_stream.hpp

```diff
 309 |+                 if (stored_ec_)
 310 |+                 {
 311 |+                     BOOST_ASIO_CORO_YIELD asio::post(this_obj_.ex_, std::move(self));
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:04:21 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413920166  

immediate completion?


📁 include/boost/mysql/pool_params.hpp

```diff
  65 |+     pool_executor_params(asio::any_io_executor pool_ex, asio::any_io_executor conn_ex = {})
  66 |+         : impl_{std::move(pool_ex), std::move(conn_ex)}
  67 |+     {
```

> Username: klemens-morgenstern  
> Created_at: 2023-12-04 14:05:28 UTC  
> Updated_at: 2023-12-04 14:13:11 UTC  
> Url: https://github.com/boostorg/mysql/pull/188#discussion_r1413921492  

I would assign pool_ex to conn_ex here, so you don't need to query later.


---
