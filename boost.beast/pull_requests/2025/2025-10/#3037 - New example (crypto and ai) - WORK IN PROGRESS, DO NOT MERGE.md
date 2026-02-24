# #3037 New example (crypto and ai) - WORK IN PROGRESS, DO NOT MERGE [Open]

> Username: MungoG  
> Created at: 2025-10-02 17:12:47 UTC  
> Updated at: 2025-12-12 19:01:37 UTC  
> Url: https://github.com/boostorg/beast/pull/3037  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 21:44:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296528990  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.cpp

```diff
   5 |+ // file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   6 |+ //
   7 |+ // Official repository: https://github.com/cppalliance
```

> Username: vinniefalco  
> Created_at: 2025-10-02 21:44:35 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400154523  

the official repo is https://github.com/boostorg/beast

> Username: MungoG  
> Created_at: 2025-10-03 08:09:18 UTC  
> Updated_at: 2025-10-03 08:09:19 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401148503  

Thank you. Updated.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 21:46:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296534001  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
  33 |+         std::chrono::system_clock::time_point,
  34 |+         double)>                                               receive_handler_;
  35 |+     std::function<void(boost::beast::error_code, char const*)> error_handler_;
```

> Username: vinniefalco  
> Created_at: 2025-10-02 21:46:10 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400158381  

Notationally, `boost::beast::error_code` has fallen out of favor. It is actually just an alias for `boost::system::error_code` so it is better to write it that way I think. In my new code I write `system::error_code` since it is usually in the `boost` namespace.

> Username: MungoG  
> Created_at: 2025-10-03 08:12:46 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401158796  

Thank you. Updated.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:27:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296855697  

📁 example/websocket/client/crypto-ai-ssl/json_price_decoder.hpp

```diff
 102 |+ #else
 103 |+ #error "Neither timegm nor _mkgmtime available"
 104 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:27:24 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400388574  

why not chrono?

> Username: MungoG  
> Created_at: 2025-10-03 09:20:57 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401334767  

Note: I have removed this code since and replaced it with Boost DateTime.  
  
However here is some background as to why this was originally done this way before I found Boost DataTime did what I needed:  
Given that chrono::from_stream is a C++20 feature, I convert the string to a chrono time_point by parsing the string into a struct tm and then converting that into a chrono::system_clock::time_point. For the conversion I have to use timegm (posix only) or _mkgmtime (windows) because std::mktime interprets the stuct tm as being in the local timezone whereas coinbase gives us strings in UTC.

> Username: vinniefalco  
> Created_at: 2025-10-03 15:37:52 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402410691  

This explanation belongs in the code and yes I see what you mean. Date formats in HTTP are a pain.

> Username: MungoG  
> Created_at: 2025-10-03 17:36:20 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402769808  

Fortunately Boost DateTime hides all the nastiness, rather than having to expose the users to this.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:28:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296857041  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.cpp

```diff
 273 |+     // Send the subscription message to the server.
 274 |+     ws_.async_write(
 275 |+         net::buffer(subscribe_json_str),
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:28:32 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400389623  

this could be a problem. the `subscribe_json_str` is destroyed when the function returns, and before the async operation completes?

> Username: MungoG  
> Created_at: 2025-10-03 17:06:54 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402683303  

Curiously, even when I try deliberately writing junk into the string's allocated buffer, the correct value is sent to the server and the subscription continues to work. I suspect somewere in the beast or asio stack the buffer data is being copied. Currently debugging to find out more.

> Username: MungoG  
> Created_at: 2025-10-03 17:23:58 UTC  
> Updated_at: 2025-10-03 17:26:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402731753  

Ah - I understand why it is currently working.  
a) With compression on, the deflation happens before the call to net::async_write, ie before websocket::async_write returns.  
b) When I turn compression off, the buffer (which is a very small subscription message) is copied here: https://github.com/boostorg/beast/blob/164db4bc57707b02550a53902cb1c138da99789f/include/boost/beast/websocket/impl/write.hpp#L320 , also before the call to net::async_write.  
  
This is clearly not something users should rely on, so I'll fix the code to persist the string throughout the lifetime of the asynchronous write operation.

> Username: MungoG  
> Created_at: 2025-10-03 17:35:21 UTC  
> Updated_at: 2025-10-03 17:35:22 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402766712  

Fixed.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:28:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296857517  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.cpp

```diff
 274 |+     ws_.async_write(
 275 |+         net::buffer(subscribe_json_str),
 276 |+         [this, len=subscribe_json_str.size()](error_code ec, std::size_t bytes_transferred)
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:28:58 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400389994  

is this initializer C++11?

> Username: vinniefalco  
> Created_at: 2025-10-02 23:29:56 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400390809  

I believe `websocket::stream::async_write` guarantees that the entire buffer will be written, there's no partial success possible

> Username: MungoG  
> Created_at: 2025-10-03 08:28:20 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401190783  

My not reading the docs correctly. Thank you. Check (and thus initializer) removed.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:30:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296859390  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.cpp

```diff
 292 |+         return error_handler_(ec, "write");
 293 |+     }
 294 |+     else if (bytes_transferred < bytes_required) {
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:30:40 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400391430  

I don't think this condition can ever be true except when `ec` indicates a failure

> Username: MungoG  
> Created_at: 2025-10-03 08:29:28 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401193112  

My not reading the docs correctly. Thank you. Check (and also unused function parameter) removed.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:32:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296861434  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.cpp

```diff
 384 |+ 
 385 |+     // Design note: the json parsing could be done without using the exception
 386 |+     // interface, but the resulting code would be considerably more verbose.
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:32:36 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400393185  

we need to fix that then, or explore alternatives


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:33:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296863265  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.hpp

```diff
   5 |+ // file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   6 |+ //
   7 |+ // Official repository: https://github.com/cppalliance
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:33:24 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400394711  

https://github.com/boostorg/beast

> Username: MungoG  
> Created_at: 2025-10-03 08:09:32 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2401149075  

Thank you. Updated.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-02 23:41:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3296873919  

📁 example/websocket/client/crypto-ai-ssl/live_price_listener.hpp

```diff
  44 |+     // function is complete. However the strand is included since it makes our threading assumptions
  45 |+     // explicit for future developers.
  46 |+     boost::asio::strand<boost::asio::io_context::executor_type> strand_;
```

> Username: vinniefalco  
> Created_at: 2025-10-02 23:41:30 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2400402773  

The purpose of a strand is not really to queue operations, it is to ensure that two operations do not execute concurrently. A subtle distinction.  
  
"we could "get away" without using a strand," is not quite true, because of composed operations.

> Username: MungoG  
> Created_at: 2025-10-03 17:24:38 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402733822  

Sorry, I don't understand. What do you mean by "composed operations" in this context?

> Username: vinniefalco  
> Created_at: 2025-10-03 17:37:21 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2402772014  

It needs an explanation too big for here

> Username: sehe  
> Created_at: 2025-10-03 21:00:03 UTC  
> Updated_at: 2025-10-03 21:00:04 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2403337990  

Tanner Sansbury touches on it explicitly here https://stackoverflow.com/questions/12794107/why-do-i-need-strand-per-connection-when-using-boostasio/12801042#12801042


---

## Comment 10

> Username: sdarwin  
> Created_at: 2025-10-03 19:44:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#issuecomment-3367002263  

please rebase on `develop`, to try ashtum's CI modifications.

---

## Review 11 [Commented]

> Username: ashtum  
> Created_at: 2025-10-09 11:46:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3318488980  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
  41 |+     class historic_fetcher_op
  42 |+     {
  43 |+         enum class state {
```

> Username: ashtum  
> Created_at: 2025-10-09 11:23:49 UTC  
> Updated_at: 2025-10-09 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416458415  

The use of `state` seems unnecessary, the majority of completion handlers have unique signature and for those that don't (like write and read) we can define a tag type and use [asio::prepend](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/prepend.html) to prepend the tag value.  
It would look like something like this:  
```C++  
struct on_write{};  
struct on_read{};  
  
// signature for write  
void operator()(  
        Self& self  
        on_write,  
        , system::error_code ec  
        , std::size_t bytes_transferred);  
  
// signature for read  
void operator()(  
        Self& self  
        on_read,  
        , system::error_code ec  
        , std::size_t bytes_transferred);  
  
// call site 1  
beast::http::async_write(  
    ssl_stream_,  
    request_,  
    asio::prepend(std::move(self), on_write{})  
);  
  
// call site 2  
beast::http::async_read(  
    ssl_stream_,  
    buffer_,  
    response_,  
    asio::prepend(std::move(self), on_read{})  
```

> Username: vinniefalco  
> Created_at: 2025-10-09 19:49:46 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417805126  

interesting technique! I hadn't thought of this. You can also use the fauxroutines (in boost/asio/coroutine.hpp)

> Username: ashtum  
> Created_at: 2025-10-10 05:50:48 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418566697  

The problem with using fauxroutines here is that we have a few incompatible function call signatures:  
```CPP  
// resolve  
void operator()(  
    Self& self  
    , system::error_code ec  
    , asio::ip::tcp::resolver::results_type results)  
  
// connect  
void operator()(  
    Self& self  
    , system::error_code ec  
    , asio::ip::tcp::resolver::results_type::endpoint_type ep)  
  
// write/read  
void operator()(  
        Self& self  
        , system::error_code ec  
        , std::size_t bytes_transferred);  
```  
  
Although technically, we can only use the fauxroutine for the write/read operations, because that's where we have to deal with more than one operation.

> Username: vinniefalco  
> Created_at: 2025-10-10 12:58:42 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2420032960  

The downside of `asio::prepend` is that it creates multiple different function template instantiations of essentially the same asio implementation code

> Username: ashtum  
> Created_at: 2025-10-10 13:35:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2420231658  

> The downside of asio::prepend is that it creates multiple different function template instantiations of essentially the same asio implementation code  
  
If I'm not mistaken, the completion token type is unique for each composed type anyway (it's essentially the Self object type). So, unless we use different tag types for the same operation, like:  
```C++  
async_write(..., asio::prepend(std::move(self), on_write1{}));  
async_write(..., asio::prepend(std::move(self), on_write2{}));  
```  
there shouldn't be any difference.

---

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
  73 |+         buffer_type&   buffer_;
  74 |+         request_type&  request_;
  75 |+         response_type& response_;
```

> Username: ashtum  
> Created_at: 2025-10-09 11:26:16 UTC  
> Updated_at: 2025-10-09 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416464403  

Would it be possible for `historic_fetcher_op` to just take a reference to `historic_fetcher`?

> Username: vinniefalco  
> Created_at: 2025-10-09 21:35:07 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418011560  

that would be better, yes

---

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 141 |+             default: {
 142 |+                 // This should not happen.
 143 |+                 throw std::logic_error("unreachable");
```

> Username: ashtum  
> Created_at: 2025-10-09 11:29:18 UTC  
> Updated_at: 2025-10-09 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416471884  

Composed operations should never throw exceptions, because the exception would be thrown from the call site of `asio::io_context::run()`. They should instead complete with an error (though in this case, it seems to be only for debugging purposes).

---

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 102 |+             request_.method(beast::http::verb::get);
 103 |+             request_.set(beast::http::field::host, client.get_host());
 104 |+             request_.set(beast::http::field::user_agent, BOOST_BEAST_VERSION_STRING);
```

> Username: ashtum  
> Created_at: 2025-10-09 11:42:26 UTC  
> Updated_at: 2025-10-09 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416507169  

It is recommended not to perform preparation work that modifies the state of the io_object (`historic_fetcher` in this case) in the constructor. This is because we can create deferred operations that are planned to be executed later, for example:  
```C++  
auto op1 = fetcher.async_historic_fetch(asio::deferred);  
auto op2 = fetcher.async_historic_fetch(asio::deferred);  
// Both of these deferred operations have called the constructor of the composed operation,  
// but neither has been scheduled for execution yet.  
```

> Username: sehe  
> Created_at: 2025-10-09 12:33:00 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416641007  

(me, taking notes; master class here)

> Username: vinniefalco  
> Created_at: 2025-10-09 19:50:41 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417807035  

didn't know that...

> Username: MungoG  
> Created_at: 2025-10-09 20:04:18 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417834767  

@ashtum I am slightly confused as to how to make the code completely robust here. Let's have a conversation in the morning if that's okay for you.

> Username: vinniefalco  
> Created_at: 2025-10-09 21:35:47 UTC  
> Updated_at: 2025-10-09 21:37:15 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418012649  

I think he means to launch the async ops in a separate member function, such as `operator()(Self& self)` ? Example: https://github.com/vinniefalco/http_io/blob/b24d776d5ac6243099828a161f8f0f9265c1b356/example/client/visit/main.cpp#L112

> Username: ashtum  
> Created_at: 2025-10-10 07:49:27 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418808267  

Yes, the preparation work should happen inside `operator()(Self& self)` instead of the constructor.

---

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 465 |+                         , response_)
 466 |+                     , token
 467 |+                     , exec);
```

> Username: ashtum  
> Created_at: 2025-10-09 11:46:07 UTC  
> Updated_at: 2025-10-09 11:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2416515637  

Instead of taking `exec` in the argument we can use the `ssl_stream_`'s executor:  
```C++  
ssl_stream_.get_executor();  
```  
In case of `asio::async_compose` you can just directly pass the io_object itself (`ssl_stream_`):  
```C++  
return asio::async_compose<  
    CompletionToken,  
    void(system::error_code)>(  
        historic_fetcher_op<Executor>(  
            *this  
            , resolver_  
            , ssl_stream_  
            , buffer_  
            , request_  
            , response_)  
        , token  
        , ssl_stream_);  
```

> Username: vinniefalco  
> Created_at: 2025-10-09 21:38:16 UTC  
> Updated_at: 2025-10-09 21:38:28 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418016391  

this does raise the question of how best to parameterize the template types. should it be `typename Executor`? `typename Stream`?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-09 19:52:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3320429116  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 141 |+             default: {
 142 |+                 // This should not happen.
 143 |+                 throw std::logic_error("unreachable");
```

> Username: vinniefalco  
> Created_at: 2025-10-09 19:52:24 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417810749  

we have `BOOST_UNREACHABLE` for this (spelling?)

> Username: MungoG  
> Created_at: 2025-10-09 19:59:36 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417825875  

Just what I was looking for, thanks (as std::unreachable is not available yet).


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-09 19:52:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3320430470  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 153 |+         {
 154 |+             if (ec) {
 155 |+                 state_ = state::error;
```

> Username: vinniefalco  
> Created_at: 2025-10-09 19:52:53 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417811808  

does this mean anything if we are completing?

> Username: MungoG  
> Created_at: 2025-10-09 19:58:42 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417824221  

Probably not. When I put this in I thought I might have to check the state of the object after io.run finished.   
  
However Mohammad's tag trick is neater than having a state (a full state machine is overkill for this and just makes the code harder to read)

> Username: vinniefalco  
> Created_at: 2025-10-09 21:38:57 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2418017224  

you should check out fauxroutines


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2025-10-09 19:53:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/3037#pullrequestreview-3320433516  

📁 example/websocket/client/crypto-ai-ssl/historic_price_fetcher.hpp

```diff
 160 |+             case state::resolving: {
 161 |+                 // Set a timeout on the operation
 162 |+                 beast::get_lowest_layer(ssl_stream_).expires_after(std::chrono::seconds(30));
```

> Username: vinniefalco  
> Created_at: 2025-10-09 19:53:51 UTC  
> Url: https://github.com/boostorg/beast/pull/3037#discussion_r2417814089  

I believe this depends on the lowest layer being `beast::basic_stream` or something like that. The stream with the built-in timeout. I would prefer we didn't use that, because it is an overly complex and cumbersome design made obsolete by asio's new "cancelation" features. We don't want to teach it and we should probably deprecate it in beast somehow.


---
