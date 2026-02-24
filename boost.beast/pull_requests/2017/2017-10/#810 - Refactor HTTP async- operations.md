# #810 [WIP] Refactor HTTP async* operations [Closed]

> Username: djarek  
> Created at: 2017-10-07 14:14:22 UTC  
> Updated at: 2019-09-10 21:02:08 UTC  
> Closed at: 2017-11-21 01:14:01 UTC  
> Url: https://github.com/boostorg/beast/pull/810  

## Changes:  
- Use stackless coroutines instead of hand-rolled state machines.  
- Added the ```is_continuation()``` helper to retrieve the state of a stackless coroutine.  
- Added the ```prepare_or_error()``` helper to avoid issues with yield labels crossing initialization of catch blocks.  
- Remove caching of mutable buffer in ```read_some_op```.  
- Renamed ```bytes_transferred``` to ```total_bytes_consumed_``` in ```read_some_op``` since it stored the number of bytes consumed by the parser.  
  
## To Do:  
- [ ] write_some_op  
- [ ] write_msg_op  
- [ ] write_op  
  
Resolves: #764

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:06:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835127  

📁 include/boost/beast/core/coroutine.hpp

```diff
  22 |+     // coroutine_ref marks the coroutine as complete if it is not modified.
  23 |+     auto state = (ref = static_cast<int>(ref));
  24 |+     return state > 0;
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:06:01 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143332923  

Oh damn, I see what you did here, this is very smart!  
  
*BUT*..... we can't be peeking into the `asio::detail::` namespace. And I don't think this should be a public interface.  
  
An alternative is to just copy all the Asio stackless coroutine code and then it becomes ours to do with as we please.  
  
If you DO add a public header file you need to add it to the "convenience header", in this case <boost/beast/core.hpp>.

> Username: vinniefalco  
> Created_at: 2017-10-07 15:19:20 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333252  

While I like the idea, we have a problem. I have another branch waiting which updates Beast to use the new version of Boost.Asio which is styled after the Networking-TS. See: https://github.com/vinniefalco/beast/commits/net-ts  
  
The new version of Boost.Asio removes the `asio_is_continuation` hook and replaces it with free functions `post` and `defer` which perform similar behaviors. But of course it completely changes the program logic. So I don't think its a good idea for us to change the `is_continuation` code otherwise we will get pretty bad merge conflicts. This change will have to wait until after the new branch is merged.  
  
That said, I like the technique of leveraging the `coroutine` state to know if we are already in the proper context. I like it so much that I think it should be a member function of `asio::coroutine`. I think you should submit it as a pull request against Boost.Asio and stand-alone Asio. If you do so, please @ mention me in it (@vinniefalco) so I can comment.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:08:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835172  

📁 include/boost/beast/http/impl/read.ipp

```diff
  38 |+ template<typename DynamicBuffer>
  39 |+ boost::optional<typename DynamicBuffer::mutable_buffers_type>
  40 |+ prepare_or_error(DynamicBuffer& buffer, error_code& ec)
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:08:16 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143332969  

I like this very much, this would be helpful to use everywhere. It should probably be available as a `detail` function, maybe in `<boost/core/detail/type_traits.hpp>`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:09:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835191  

📁 include/boost/beast/http/impl/read.ipp

```diff
  42 |+     try
  43 |+     {
  44 |+         ec.assign(0, ec.category());
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:09:10 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143332986  

If there's an error we are paying for clearing `ec` needlessly. It might be would be more efficient to clear the error if the call to `prepare` succeeds. We would have to study the output in Compiler Explorer (https://godbolt.org/)

> Username: djarek  
> Created_at: 2017-10-08 11:00:07 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351673  

I'll check it out, though it's worth remembering the the most likely case is that there is no error. And if there is one, we've wasted 2 machine word writes, so I don't think this is a huge issue.

> Username: vinniefalco  
> Created_at: 2017-10-08 13:25:01 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143354615  

True

> Username: djarek  
> Created_at: 2017-10-15 20:35:46 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r144731053  

I've checked it and the difference is 1 assignment. One thing I'm wondering about is how should a composed operation behave if prepare() throws a different error (e.g. bad_alloc)?

> Username: vinniefalco  
> Created_at: 2017-10-15 21:50:14 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r144733156  

> how should a composed operation behave if prepare() throws a different error (e.g. bad_alloc)  
  
I think it should propagate the exception as normal.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:09:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835203  

📁 include/boost/beast/http/impl/read.ipp

```diff
  48 |+     {
  49 |+         ec = error::buffer_overflow;
  50 |+         return {};
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:09:45 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143332997  

Does this return `boost::none` or does it return an engaged, default-constructed `mutable_buffers_type`?

> Username: djarek  
> Created_at: 2017-10-08 10:58:16 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351636  

Returns an empty optional. This notation works here just like in mathematics: "return empty set(nothing)". Besides, ```mutable_buffers_type``` is not default constructible.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:10:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835212  

📁 include/boost/beast/http/impl/read.ipp

```diff
  45 |+         return {buffer.prepare(read_size_or_throw(buffer, 65536))};
  46 |+     }
  47 |+     catch(const std::length_error& error)
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:10:08 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333003  

`error` is the name of a public symbol, FYI, you might want to rename it to `e`

> Username: djarek  
> Created_at: 2017-10-08 10:58:06 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351634  

Got rid of the name, wasn't necessary


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:11:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835248  

📁 include/boost/beast/http/impl/read.ipp

```diff
 135 |+     // Temporary variables, not to be used across yields.
 136 |+     // They have to be declared here due to initialization & yield issues.
 137 |+     size_t consumed = 0;
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:11:52 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333043  

`std::size_t`


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:20:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835474  

📁 include/boost/beast/http/impl/read.ipp

```diff
  19 | #include <boost/beast/core/read_size.hpp>
  20 | #include <boost/beast/core/type_traits.hpp>
  21 |+ #include <boost/beast/core/coroutine.hpp>
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:20:33 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333273  

I don't think this should be public (and it has other problems mentioned earlier) but for future reference when we add new public interfaces, it is best done in its own individual commit so that people who look at the changes will know that there's a new feature. Also, the docs need to be updated (quickref.xml)

> Username: djarek  
> Created_at: 2017-10-08 11:04:44 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351752  

Yeah, you're right, it doesn't belong in the public API. I'll move the is_continuation function to asio instead.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:22:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835505  

📁 include/boost/beast/http/impl/read.ipp

```diff
 120 |+     {
 121 |+         s_.get_io_service().post(
 122 |+             bind_handler(std::move(*this), ec, 0));
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:22:15 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333295  

I prefer to do this explicitly inside the main function rather than as a separate function. Also I would not mark it `const`. Beast does not interpret `const` as a "bitwise const" as you have done here. Instead, `const` is roughly interpreted as "thread safe" - which this function is not.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:22:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835512  

📁 include/boost/beast/http/impl/read.ipp

```diff
 115 |-     switch(state_)
 135 |+     // Temporary variables, not to be used across yields.
 136 |+     // They have to be declared here due to initialization & yield issues.
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:22:51 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333306  

I know you mean well but this comment is not necessary, as we can assume the reader already understands the price of using stackless coroutines.

> Username: djarek  
> Created_at: 2017-10-08 11:00:51 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351690  

Done.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:23:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835521  

📁 include/boost/beast/http/impl/read.ipp

```diff
 159 |-             mb_.emplace(b_.prepare(
 160 |-                 read_size_or_throw(b_, 65536)));
 152 |+             BOOST_ASIO_CORO_YIELD do_upcall(ec);
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:23:16 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333314  

I would prefer to put `BOOST_ASIO_CORO_YIELD` on its own line


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835536  

📁 include/boost/beast/http/impl/read.ipp

```diff
 153 |         }
 162 |-         catch(std::length_error const&)
 154 |+         while (!ec && total_bytes_consumed_ == 0)
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:07 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333327  

I think the order should be reversed, i.e.  
```  
while(total_bytes_consumed_ == 0 && ! ec)  
```  
  
Rationale: `total_bytes_consumed_` will be non zero far more frequently than `ec` will be set.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835550  

📁 include/boost/beast/http/impl/read.ipp

```diff
 157 |+             if (ec)
 158 |+             {
 159 |+                 BOOST_ASIO_CORO_YIELD do_upcall(ec);
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:40 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333342  

I would prefer to put BOOST_ASIO_CORO_YIELD on its own line. And I don't think we should have `do_upcall` as a separate function I think its more clear just inlining the call to `io_service::post`.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835555  

📁 include/boost/beast/http/impl/read.ipp

```diff
 176 |-     case 3:
 177 |-         break;
 163 |+             BOOST_ASIO_CORO_YIELD s_.async_read_some(*mb, std::move(*this));
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:24:49 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333347  

Separate line please


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:25:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835566  

📁 include/boost/beast/http/impl/read.ipp

```diff
 179 |+             {
 180 |+                 // No need to call do_upcall() - we're already being executed
 181 |+                 // within the io_service's context.
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:25:19 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333363  

This comment is not necessary, the reader should already know this.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:25:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835571  

📁 include/boost/beast/http/impl/read.ipp

```diff
 189 |+             {
 190 |+                 ec.assign(0, ec.category());
 191 |+             }
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:25:33 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333367  

I would not use braces since it is a single statement.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:26:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835591  

📁 include/boost/beast/http/impl/read.ipp

```diff
 291 |-             state_ = 1;
 292 |-             return s_.get_io_service().post(
 303 |+             BOOST_ASIO_CORO_YIELD s_.get_io_service().post(
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:26:33 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333384  

The problem with putting this all on one line is that it is harder to set breakpoints with visual studio, and it also moves the important part farther to the right in the editor. I try to keep the code narrow, it helps when I screenshare and do collaborative code reviews.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:27:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835617  

📁 include/boost/beast/http/impl/read.ipp

```diff
 432 |+         // Can't call the handler within the coroutine body
 433 |+         // because the data struct would be destroyed before
 434 |+         // we left the coroutine, causing use-after-free.
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:27:43 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333408  

Did you add this comment? How did you figure this out?

> Username: djarek  
> Created_at: 2017-10-08 11:03:06 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143351729  

Yes I did, after I ran into a segfault in tests. I used asan to figure it out.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-07 15:32:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67835712  

📁 include/boost/beast/http/impl/read.ipp

```diff
 441 |-     bytes_transferred = d.bytes_transferred;
 442 |-     d_.invoke(ec, bytes_transferred);
 430 |+     if (d.is_complete())
```

> Username: vinniefalco  
> Created_at: 2017-10-07 15:32:23 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143333516  

This performs an unnecessary call to `is_complete` on every continuation. Consider this instead:  
```  
    }  
    return;  
upcall:  
    d_.invoke(ec, bytes_transferred);  
}  
```  
  
And use `goto upcall` to invoke the final completion handler


---

## Comment 19

> Username: vinniefalco  
> Created_at: 2017-10-07 15:34:41 UTC  
> Url: https://github.com/boostorg/beast/pull/810#issuecomment-334943738  

I greatly appreciate your contribution, it is wonderful to see the community getting involved in the project. This is going to really help everyone since it will enable me to get even more done with the new libraries I am building on top of Beast. However, there's a learning curve to the style used to write the composed operations as you can see from my comments. Please don't let that discourage you!  
  
I think that `prepare_or_error` should be in its own commit (in `boost::beast::detail::`) and come first, it looks quite the useful function and something that I will be adopting in new code.

---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-08 20:04:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67867319  

📁 include/boost/beast/http/impl/read.ipp

```diff
 128 | {
 115 |-     switch(state_)
 129 |+     size_t consumed = 0;
```

> Username: vinniefalco  
> Created_at: 2017-10-08 20:04:42 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143364946  

You don't **have** to put this here, you could put it in a braced scope where it is used:  
```  
{  
    auto const consumed = p_.put(b_.data(), ec);  
    total_bytes_consumed_ += consumed;  
    b_.consume(consumed);  
}  
```

> Username: vinniefalco  
> Created_at: 2017-10-30 19:31:49 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r147810477  

Should be `std::size_t`


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-08 20:08:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67867415  

📁 include/boost/beast/http/impl/read.ipp

```diff
 139 |-             ec = error::end_of_stream;
 140 |-             goto upcall;
 144 |+             BOOST_ASIO_CORO_YIELD
```

> Username: vinniefalco  
> Created_at: 2017-10-08 20:08:26 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143365072  

I think you might need braces here, because `BOOST_ASIO_CORO_YIELD` is a compound statement:  
```  
if (ec == http::error::need_more)  
{  
    BOOST_ASIO_CORO_YIELD  
    s_.get_io_service().post(  
        bind_handler(std::move(*this), ec, 0));  
}  
```  
  
This is why I indent it the way I do, I treat `BOOST_ASIO_CORO_YIELD` as if it was a separate statement:  
https://github.com/boostorg/beast/blob/0bc2a414043f4a9430e71e6664d592e67ea6d282/include/boost/beast/websocket/impl/read.ipp#L162


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2017-10-08 20:09:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/810#pullrequestreview-67867435  

📁 include/boost/beast/http/impl/read.ipp

```diff
 154 |+                     s_.get_io_service().post(
 155 |+                         bind_handler(std::move(*this), ec, 0));
 156 |+                 break;
```

> Username: vinniefalco  
> Created_at: 2017-10-08 20:09:13 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r143365092  

Is the meaning of `break` defined for stackless coroutines?

> Username: djarek  
> Created_at: 2017-10-14 09:47:36 UTC  
> Updated_at: 2017-10-24 22:21:47 UTC  
> Url: https://github.com/boostorg/beast/pull/810#discussion_r144690601  

```BOOST_ASIO_CORO_YIELD break;``` terminates the coroutine.  
```break;``` outside of a yield statement refers to the logical block you're in (if you're in a loop, it will break out of it).


---
