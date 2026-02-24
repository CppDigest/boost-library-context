# #283 Removes async_append_some [Merged]

> Username: mzimbres  
> Created at: 2025-07-10 13:14:20 UTC  
> Updated at: 2025-07-22 18:51:50 UTC  
> Merged at: 2025-07-22 18:51:50 UTC  
> Closed at: 2025-07-22 18:51:50 UTC  
> Url: https://github.com/boostorg/redis/pull/283  

@anarthal Before I start investigating how to proceed with @D0zee PR I would like to merge this one which simplifies how the read buffer is used.

---

## Review 1 [Commented]

> Username: anarthal  
> Created_at: 2025-07-10 16:10:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/283#pullrequestreview-3006196546  

📁 include/boost/redis/detail/read_buffer.hpp

```diff
  25 |+ 
  26 |+    [[nodiscard]]
  27 |+    auto get_append_buffer() noexcept -> std::pair<char*, std::size_t>;
```

> Username: anarthal  
> Created_at: 2025-07-10 15:14:34 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198029330  

Consider replacing by `boost::span<char>`

> Username: mzimbres  
> Created_at: 2025-07-10 20:39:30 UTC  
> Updated_at: 2025-07-10 20:39:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198681321  

This is not public, so I avoided pulling in another boost dependency.

---

📁 include/boost/redis/detail/read_buffer.hpp

```diff
  15 |+ namespace boost::redis::detail {
  16 |+ 
  17 |+ class read_buffer {
```

> Username: anarthal  
> Created_at: 2025-07-10 15:24:17 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198055010  

I suggest adding a test_read_buffer unit test that covers these functions, even if they're small

---

📁 include/boost/redis/detail/read_buffer.hpp

```diff
  17 |+ class read_buffer {
  18 |+ public:
  19 |+    void prepare_append(
```

> Username: anarthal  
> Created_at: 2025-07-10 15:27:34 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198062975  

Suggest returning the error code with [[nodiscard]]

---

📁 include/boost/redis/detail/read_buffer.hpp

```diff
  31 |+ 
  32 |+    [[nodiscard]]
  33 |+    auto get_committed_size() const noexcept -> std::size_t;
```

> Username: anarthal  
> Created_at: 2025-07-10 15:39:28 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198096028  

Isn't this just `get_committed_buffer().size()`? Do we need it?

> Username: mzimbres  
> Created_at: 2025-07-10 20:32:34 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198667006  

Yeah, but it avoids creating a `string_view` just to read its size, which is a readly available member of the `read_buffer`.

---

📁 include/boost/redis/detail/read_buffer.hpp

```diff
  38 |+ 
  39 |+ private:
  40 |+    std::string buffer_;
```

> Username: anarthal  
> Created_at: 2025-07-10 16:02:24 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198145604  

Might be better represented as `std::vector<char>`, instead. SBO here hurts more than it helps, and the contents are semantically closer to an array of bytes than a string (i.e. traits here is irrelevant).


📁 include/boost/redis/config.hpp

```diff
  95 |     */
  96 |-    std::size_t max_read_size = (std::numeric_limits<std::size_t>::max)();
  96 |+    std::size_t max_read_buffer_size = (std::numeric_limits<std::size_t>::max)();
```

> Username: anarthal  
> Created_at: 2025-07-10 15:21:05 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198046920  

This is a braking change, is there a possibility to maintain the old name?


📁 include/boost/redis/detail/reader_fsm.hpp

```diff
  63 |+    config cfg_;
  64 |    action action_after_resume_;
  65 |    action::type next_read_type_ = action::type::append_some;
```

> Username: anarthal  
> Created_at: 2025-07-10 15:35:32 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198086158  

Why do we need a needs_more action and a next_read_type_? It looks like it has the same semantics as append_some.  
  
append_some should likely be renamed to read_some

> Username: mzimbres  
> Created_at: 2025-07-10 20:29:29 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198659964  

needs_more is useful in some situations  
1. Testing: I know that the fsm is being suspended exactly [here](https://github.com/boostorg/redis/blob/adf17f2b3b2eb119abe324f82dc93cea0a2d0065/include/boost/redis/impl/reader_fsm.ipp#L47).  
2. Logging: Most responses will be contained entirely in the buffer when parsing starts. I would however like to see in the log when parsing has to be suspended because the current message is incomplete and more is needed.  
  
> append_some should likely be renamed to read_some  
  
That is reasonable, I can change it.

---

📁 include/boost/redis/detail/reader_fsm.hpp

```diff
  60 | private:
  61 |    int resume_point_{0};
  62 |+    read_buffer read_buffer_;
```

> Username: anarthal  
> Created_at: 2025-07-10 15:37:38 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198091453  

I'd make read_buffer_ a property of the connection, and pass here a pointer. This way, FSMs are kept non-owning. This way, you don't need reset(), and can just re-create the FSM every time, making it impossible to create bugs by forgetting members in reset().

> Username: mzimbres  
> Created_at: 2025-07-10 20:41:09 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198685497  

Yeah, also considered this but did not go so far because I had change so much code already.

---

📁 include/boost/redis/detail/reader_fsm.hpp

```diff
  64 |    action action_after_resume_;
  65 |    action::type next_read_type_ = action::type::append_some;
  66 |    multiplexer* mpx_ = nullptr;
```

> Username: anarthal  
> Created_at: 2025-07-10 15:44:20 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198105774  

As written, this yields to a dangling pointer after a connection is moved. I suggest wrapping every member in `basic_connection` into a state object, and placing this into the heap via a `unique_ptr` so they're guaranteed to have stable addresses.


📁 include/boost/redis/detail/multiplexer.hpp

```diff
  16 | 
  17 |- #include <boost/asio/experimental/channel.hpp>
  17 |+ #include <boost/system.hpp>
```

> Username: anarthal  
> Created_at: 2025-07-10 15:45:53 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198109060  

Please avoid whole-lib includes


📁 include/boost/redis/impl/multiplexer.ipp

```diff
  98 |    if (!on_push_)  // Prepare for new message.
  97 |-       on_push_ = is_next_push();
  99 |+       on_push_ = is_next_push(data);
```

> Username: anarthal  
> Created_at: 2025-07-10 15:56:51 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198135074  

This is likely not something to fix in this PR, but isn't the multiplexer violating point 1. in the comment above? While we're parsing something that's not a push and we run out of data, on_push_ will be false and we will look at the message's first byte even if we shouldn't.

> Username: mzimbres  
> Created_at: 2025-07-10 20:42:16 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198688036  

I have to review this again and test it. I remember it being trick see comments in `is_next_push`.

---

📁 include/boost/redis/impl/multiplexer.ipp

```diff
 229 |-       usage_.push_bytes_received += parser_.get_consumed();
 244 |+       usage_.push_bytes_received += size;
 245 |+       on_push_ = false;
```

> Username: anarthal  
> Created_at: 2025-07-10 16:00:27 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198142083  

commit_usage setting on_push looks semantically surprising

> Username: mzimbres  
> Created_at: 2025-07-10 20:37:21 UTC  
> Updated_at: 2025-07-10 20:37:22 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198678025  

Yeah, it is not the correct place but it works well because `commit_usage` is called only once when finished with an individual response. This has to be moved elsewhere.


📁 test/common.hpp

```diff
  37 |    boost::redis::operation op = boost::redis::operation::receive);
  38 |+ 
  39 |+ void append_read_data(boost::redis::detail::reader_fsm& fsm, std::string const& data);
```

> Username: anarthal  
> Created_at: 2025-07-10 16:02:55 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198146535  

string_view?


📁 test/common.cpp

```diff
  72 |+ void append_read_data(boost::redis::detail::reader_fsm& fsm, std::string const& data)
  73 |+ {
  74 |+    auto const buffer = fsm.get_append_buffer();
```

> Username: anarthal  
> Created_at: 2025-07-10 16:07:42 UTC  
> Updated_at: 2025-07-10 16:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/283#discussion_r2198156113  

maybe BOOST_ASSERT that we don't exceed the buffer size?


---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-07-20 17:14:47 UTC  
> Url: https://github.com/boostorg/redis/pull/283#issuecomment-3094657176  

@anarthal I think I addressed all your comments. Your other PR will fix the remaining problems.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-07-22 14:02:46 UTC  
> Url: https://github.com/boostorg/redis/pull/283#issuecomment-3102887216  

Looks good to me. I'll merge mine after you merge this.

---
