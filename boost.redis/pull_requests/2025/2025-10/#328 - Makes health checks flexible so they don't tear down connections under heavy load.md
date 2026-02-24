# #328 Makes health checks flexible so they don't tear down connections under heavy load [Merged]

> Username: anarthal  
> Created at: 2025-10-09 10:27:37 UTC  
> Updated at: 2025-10-20 19:50:44 UTC  
> Merged at: 2025-10-20 13:29:21 UTC  
> Closed at: 2025-10-20 13:29:21 UTC  
> Url: https://github.com/boostorg/redis/pull/328  

Adds `error::write_timeout`  
  
close #104

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-09 10:36:16 UTC  
> Url: https://github.com/boostorg/redis/pull/328#issuecomment-3385246149  

@mzimbres this is a very early prototype, and probably far from the implementation you were expecting. I want to have some discussion to make sure the direction I've taken doesn't have any flaws.  
  
The idea is that the health checker task disappears, with health checks performed by the writer and reader directly.  
  
On the writer side:  
  
* When there is data to be written, the writer writes it and does no health checks.  
* If more than `config::health_check_interval` elapses without anything to write, a PING is added to the multiplexer. The PING is represented as a `multiplexer::elem` but does not go through `async_exec`.  
* When writing, if we spend more than `config::health_check_interval` without writing a single byte, we consider the connection as broken. (Actually this requires a little bit more effort from what I've written, but should be easy to do).  
  
On the reader side:  
  
* We know that the connection may stay idle for at most `config::health_check_interval`. After that time, a ping is issued. If we don't receive any data within that period plus `config::health_check_interval`, the connection is definitely dead. This leaves us a read timeout of `2* config::health_check_interval`. This is a little bit of an heuristic, but I think is good enough.  
* If a PING containing an error is received, its adapter yields an error. This stops the reader and triggers a reconnection.  
  
Let me know what you think.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-16 16:07:35 UTC  
> Updated_at: 2025-10-16 16:09:05 UTC  
> Url: https://github.com/boostorg/redis/pull/328#issuecomment-3411619566  

I've kept the `cancel_after` in the reader approach. I think it is roughly equivalent to checking a timestamp updated by the reader. If I understood your approach, we'd do the following:  
  
* We have a timestamp in the connection's state, updated by the reader every time some data is received (i.e. after an `async_read_some` completes).  
* If no data is received for a certain time (let's call this value `read_timeout`), the connection is considered unhealthy and re-connected.  
* The writer checks after every `async_write_some` operation, and after waiting for more data to be available, whether the timestamp is older than `read_timeout`. If it is, the connection should fail.  
  
My line of thought is that this is really imposing a timeout to reads, but implemented using polling. I think it can be more unreliable than just using `asio::cancel_after`.  
  
I've chosen this `read_timeout` to be `2 * health_check_interval`. When the connection is idle, no data is read or written during `health_check_interval`. A PING will be issued after `health_check_interval`. This leaves the server another `health_check_interval` to respond to the PING.  
  
I'm open to changing the implementation if there is something I've missed here, let me know.  
  
I've also updated the reader and writer actions to be variant-like, so they take less stack space. I've used unions with a lean class interface to make it lighter at compile time.

---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-16 20:43:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/328#pullrequestreview-3346946309  

📁 include/boost/redis/config.hpp

```diff
  86 |-    /// Message used by the health-checker in @ref boost::redis::basic_connection::async_run.
  86 |+    /// Message used by `PING` commands sent by the health checker.
  87 |    std::string health_check_id = "Boost.Redis";
```

> Username: mzimbres  
> Created_at: 2025-10-16 20:43:38 UTC  
> Updated_at: 2025-10-16 20:43:39 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2437422347  

Sidenote: When the user does not provide one id, I think we should format this to contain the id returned by the `HELLO` command.

> Username: anarthal  
> Created_at: 2025-10-17 10:06:25 UTC  
> Updated_at: 2025-10-17 10:06:26 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2439128117  

What does it mean "does not provide one id" here? Leave `health_check_id` to the default? Make `health_check_id` empty?  
  
The change is not trivial because it requires coordination between the setup task (which may not contain a HELLO at all) and the health checker.

> Username: mzimbres  
> Created_at: 2025-10-18 08:20:15 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2441784322  

I mean, if the user does not provide its own `health_check_id` the connection could set it to "Boost.Redis (conn-id)". At the moment we only set to "Boost.Redis".

> Username: anarthal  
> Created_at: 2025-10-20 19:50:44 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2445961960  

We'd need to detect whether the setup request contains a hello, and then parse the response and update the ping ID. Do you think it's worth it?


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-16 20:51:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/328#pullrequestreview-3346968504  

📁 include/boost/redis/connection.hpp

```diff
 240 |+             } else {
 241 |+                conn->stream_.async_write_some(buf, std::move(self));
 242 |+             }
```

> Username: mzimbres  
> Created_at: 2025-10-16 20:51:38 UTC  
> Updated_at: 2025-10-16 20:52:21 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2437438762  

Can we unify the two `async_write_some` calls into one by creating an infinite interval when `timeout == 0`?

> Username: anarthal  
> Created_at: 2025-10-17 10:22:40 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2439204055  

We can't use a max interval because that causes an integer overflow and UB. `cancel_after` computes a `time_point expiry = now() + timeout`. What we could do is using `cancel_at` and the following logic:  
  
```cpp  
asio::cancel_at(timeout.count() == 0 ? (std::chrono::steady_clock::time_point::max)() : std::chrono::steady_clock::now() + timeout)  
```  
  
`cancel_at` does cause an allocation, so technically not calling it is lighter than calling it with an infinite time point. I guess calling `async_write_some` with both `cancel_after` and without it causes additional instantiations, increasing build times and executable size. So there's a trade-off here. I can go with the solution I proposed above.


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-16 20:55:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/328#pullrequestreview-3346978233  

📁 include/boost/redis/connection.hpp

```diff
 248 |+             } else {
 249 |+                conn->writer_cv_.expires_at((std::chrono::steady_clock::time_point::max)());
 250 |+             }
```

> Username: mzimbres  
> Created_at: 2025-10-16 20:55:32 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2437446551  

To avoid this `if - else` here I would move this logic into an action member function and call   
```cpp  
conn->writer_cv_.expires_at(act.get_interval())  
```

> Username: anarthal  
> Created_at: 2025-10-17 10:24:53 UTC  
> Updated_at: 2025-10-17 10:24:54 UTC  
> Url: https://github.com/boostorg/redis/pull/328#discussion_r2439213114  

`expires_at` requires a `time_point` rather than an interval (duration). We could return a `time_point` in the action, but I'm not too comfortable with it because that requires reading the clock in the FSM, and it complicates testing a lot (there's no easy way to mock reading the clock). What I could do is re-use the approach I proposed above, factoring into a function, to avoid the conditional.


---

## Comment 6

> Username: mzimbres  
> Created_at: 2025-10-16 21:26:08 UTC  
> Url: https://github.com/boostorg/redis/pull/328#issuecomment-3412920477  

I am still studying the code but I think the writter-offset should be moved down to the multiplexer where the writer buffer is located. The writer-fsm can then call `mpx.commit_write_some(bytes)`. Then the writer_op wouldn't need this  
```cpp  
auto buf = asio::buffer(conn->st_.mpx.get_write_buffer().substr(act.write_offset()));  
```  
because the multiplexer knowns about the offset, so `conn->st_.mpx.get_write_buffer()` would already be correct.

---

## Comment 7

> Username: anarthal  
> Created_at: 2025-10-17 10:25:28 UTC  
> Url: https://github.com/boostorg/redis/pull/328#issuecomment-3414854166  

> I am still studying the code but I think the writter-offset should be moved down to the multiplexer where the writer buffer is located. The writer-fsm can then call `mpx.commit_write_some(bytes)`. Then the writer_op wouldn't need this  
>   
> ```c++  
> auto buf = asio::buffer(conn->st_.mpx.get_write_buffer().substr(act.write_offset()));  
> ```  
>   
> because the multiplexer knowns about the offset, so `conn->st_.mpx.get_write_buffer()` would already be correct.  
  
I think this is a very good idea. I will work on it.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-10-17 11:24:36 UTC  
> Url: https://github.com/boostorg/redis/pull/328#issuecomment-3415083628  

Comments applied.

---
