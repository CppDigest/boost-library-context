# #1783 Resubmission of the socks example code [Closed]

> Username: madmongo1  
> Created at: 2019-12-18 08:32:41 UTC  
> Updated at: 2020-04-28 18:26:13 UTC  
> Closed at: 2020-04-28 18:26:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 13:58:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333986585  

📁 example/contrib/socks-client/socks/detail/char_type.hpp

```diff
 109 |+ to_hex(unsigned char c) noexcept
 110 |+ {
 111 |+     static const char* hexstring[] = {
```

> Username: vinniefalco  
> Created_at: 2019-12-18 13:58:54 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359356408  

This table could be made 1/3rd smaller by not inserting nulls, e.g.:  
```  
static char const hex[] = "00" "01" "02" "03" ...  
```  
(Note how there are no commas). I'm not sure I care enough about it to change it but I just wanted to point it out. If and when we make this a public API then yeah I would change it.

> Username: madmongo1  
> Created_at: 2019-12-19 08:14:43 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359731939  

label: comment-1

> Username: viccpp  
> Created_at: 2019-12-19 08:18:25 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359733184  

Or `const char hexstring[][2] = {` with commas

> Username: madmongo1  
> Created_at: 2019-12-19 09:58:50 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359775934  

b08272525 (HEAD -> socks-example, origin/socks-example) comment-1 : detail::to_hex now implemented in terms of continuous string. Added a test


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:00:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333987945  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  27 |+ async_handshake_v4(
  28 |+     AsyncStream& stream,
  29 |+     const std::string& hostname,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:00:47 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359357364  

should use `string_view` instead of `std::string const&`

> Username: madmongo1  
> Created_at: 2019-12-19 10:05:53 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359779246  

Interesting. The implementation stores a string in any case. From a stylstic persepctive I would support an argument for "pass string by value, move into place". In reality it will make no difference but conceptually it can avoid copying a long string.  
  
In any case, will change as advised.

> Username: madmongo1  
> Created_at: 2019-12-19 10:55:40 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359801375  

label: comment-2

> Username: vinniefalco  
> Created_at: 2019-12-19 12:48:08 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359842786  

I'm still not convinced it needs to store any string at all

> Username: madmongo1  
> Created_at: 2019-12-19 14:06:46 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359875705  

@vinniefalco Strictly correct for socks 4, socks 5 does not submit the hostname until after authentication, so the impl would need to either cache the supplied hostname or pre-build the buffer for submission in state 5. Similarly, auth credentials are not submitted in socks5 until state 2 so some buffering required there.  
  
Storing a string is minutely inefficient, the tradeoff being maintainability (and the ability to add better debugging/loging logic in future if desired).  
  
In production code I'd store the string, as I know at some point I'm going to want to add some logging or diagnostics. In library code where we are shipping shrink-wrapped guaranteed-correct black boxes, the 'correct' approach is probably a matter of opinion.  
  
I will defer to your decision. You have a lot more experience in the maintenance of public libraries.

> Username: vinniefalco  
> Created_at: 2019-12-19 14:57:04 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359902286  

Hmm...didn't know that about SOCKSv5. But just to keep things spicy, if `std::string` uses the small buffer optimization then passing it as a buffer to I/O routines from a composed operation could result in undefined behavior if the string is moved (when the composed operation is submitted, e.g. `std::move(*this)`). Up to you to decide what you want to do. I would say, go for whatever is expedient. It is more important we get this merged than to make it "perfect."


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:01:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333988229  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  22 |+ template<
  23 |+     typename AsyncStream,
  24 |+     typename Handler
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:01:15 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359357600  

This is missing the default-completion-token stuff

> Username: madmongo1  
> Created_at: 2019-12-20 10:39:24 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360317604  

label: comment-3


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:02:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333989193  

📁 example/contrib/socks-client/socks/impl/error.ipp

```diff
  17 |+ namespace detail {
  18 |+ 
  19 |+ class error_codes : public error_category
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:02:39 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359358357  

There's a better way to structure this code:  
https://github.com/vinniefalco/json/blob/develop/include/boost/json/impl/error.ipp#L19  
You tuck it all into the function. It isn't urgent to change this now but FYI.

> Username: madmongo1  
> Created_at: 2019-12-20 17:29:04 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360479713  

so done.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:03:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333989820  

📁 example/contrib/socks-client/socks/impl/error.ipp

```diff
  26 |+     }
  27 |+ 
  28 |+     BOOST_BEAST_DECL
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:03:35 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359358810  

I would actually like to know if derived overrides need the `__declspec(DLLEXPORT)` (or whatever).


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:17:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333999714  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  13 |+ #include <socks/detail/protocol.hpp>
  14 |+ #include <boost/beast/core/async_base.hpp>
  15 |+ #include <boost/beast/core/detail/is_invocable.hpp>
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:17:47 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359366363  

Questionable, since it is a private include. No change advised but keep an eye on this.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:17:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-333999880  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  16 |+ #include <boost/config.hpp> // for BOOST_FALLTHROUGH
  17 |+ #include <boost/asio/ip/tcp.hpp>
  18 |+ #include <boost/asio/streambuf.hpp>
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:17:59 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359366480  

asio streambuf is meh


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:18:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334000226  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  41 |+ 
  42 |+ template<typename type, typename target>
  43 |+ void write(type v, target& p)
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:18:25 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359366738  

These unconstrained read/write functions are dangerous.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:18:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334000515  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  53 |+     class Handler,
  54 |+     class Buffer,
  55 |+     class base_type = boost::beast::async_base<
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:18:48 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359366954  

This `base_type` parameter is unnecessary


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:19:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334000727  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  62 |+ 
  63 |+     socks4_op(Stream& stream, Handler& handler,
  64 |+         const std::string& hostname, unsigned short port,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:19:05 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359367107  

Should be `string_view`

> Username: madmongo1  
> Created_at: 2019-12-20 17:29:48 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360480338  

updated.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:21:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334002962  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  90 |+         case 0:
  91 |+         {
  92 |+             step_ = 1;
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:21:55 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359368776  

All these things (except the async_write) could have been done in the constructor instead, and then we could have fewer data members.

> Username: madmongo1  
> Created_at: 2019-12-20 17:30:01 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360480512  

done


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:22:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334003456  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  86 |+         auto& request = *request_;
  87 |+ 
  88 |+         switch (ec ? 3 : step_)
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:22:33 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359369181  

This needs to use `boost::asio::coroutine` instead of a hand-rolled state machine

> Username: madmongo1  
> Created_at: 2019-12-20 17:30:22 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360480787  

done for socks 4. in progress for socks5


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:24:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334004651  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 159 |+     Stream& stream_;
 160 |+ 
 161 |+     using BufferPtr = std::unique_ptr<Buffer>;
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:24:05 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359370146  

This is such a straightforward implementation, I don't see the utility in letting `Buffer` be a configurable type. It doesn't need to go into a unique_ptr.

> Username: madmongo1  
> Created_at: 2019-12-20 17:30:38 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360481017  

resolved


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:24:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334005162  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 468 |+                     read<uint16_t>(resp));
 469 |+ 
 470 |+                 std::cout << "* SOCKS remote host: " << remote_endp.address().to_string()
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:24:45 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359370532  

\me raises eyebrows

> Username: madmongo1  
> Created_at: 2019-12-20 17:31:34 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360481726  

will be removed in the refactor.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:26:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334006472  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 404 |+ 
 405 |+             auto resp = static_cast<const char*>(response.data().data());
 406 |+             auto ver = read<uint8_t>(resp);
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:26:26 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359371581  

All this code and other business logic needs to be factored out into individual implementation-specific functions.

> Username: madmongo1  
> Created_at: 2019-12-20 17:31:52 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360481986  

done in socks 4. pending for socks 5


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:27:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334007040  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 457 |+                 for (int i = 0; i < domain_length; i++)
 458 |+                     domain.push_back(read<uint8_t>(resp));
 459 |+                 auto port = read<uint16_t>(resp);
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:27:11 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359372026  

I don't understand, this code does nothing with `domain` afterwards?


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:28:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334007959  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 384 |+             return net::async_write(
 385 |+                 stream_,
 386 |+                 request,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:28:26 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359372778  

Are these using the Beast dynamic buffers? They have different semantics from the Asio ones, we need to be careful here.

> Username: madmongo1  
> Created_at: 2019-12-20 17:32:28 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360482495  

all buffers being replaced with one re-usable buffer string


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:30:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334009764  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 588 |+         net::associated_allocator_t<HandlerType>>:: template rebind_alloc<char> >;
 589 |+ 
 590 |+     socks5_op<AsyncStream, HandlerType, Buffer>(stream, init.completion_handler,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:30:47 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359374176  

This needs to use `async_initiate`


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:30:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334009864  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 558 |+         net::associated_allocator_t<HandlerType>>:: template rebind_alloc<char> >;
 559 |+ 
 560 |+     socks4_op<AsyncStream, HandlerType, Buffer>(stream, init.completion_handler,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:30:55 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359374259  

This needs to use `async_initiate`


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-18 14:31:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-334009931  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 546 |+     const std::string& hostname,
 547 |+     unsigned short port,
 548 |+     std::string const& username,
```

> Username: vinniefalco  
> Created_at: 2019-12-18 14:31:01 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359374309  

`string_view`

> Username: madmongo1  
> Created_at: 2019-12-18 15:02:40 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359392851  

Great feedback. All actionable. I'll get on it.

> Username: vinniefalco  
> Created_at: 2019-12-18 16:19:54 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r359438060  

It looks like there's stuff missing. Based on what I'm seeing, we will probably need to make several passes over it.


---

## Comment 21

> Username: codecov[bot]  
> Created_at: 2019-12-18 16:07:37 UTC  
> Updated_at: 2019-12-22 19:52:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#issuecomment-567098372  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=h1) Report  
> Merging [#1783](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/9be923c40e09cef97d23a3b93161458ade0280b8?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1783/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1783      +/-   ##  
===========================================  
+ Coverage    95.16%   95.17%   +0.01%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
+ Hits         11371    11373       +2       
+ Misses         578      576       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1783/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0%> (+0.19%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1783/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `97.32% <0%> (+0.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=footer). Last update [9be923c...bdbcf10](https://codecov.io/gh/boostorg/beast/pull/1783?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 22

> Username: madmongo1  
> Created_at: 2019-12-20 10:45:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#issuecomment-567881656  

@vinniefalco   
  
Breaking: I think there is a design error in the handshake functions.   
The internal operation creates and discards its own read buffer.  
This opens up the possibility of the internal op performing a read which acquires more bytes than is needed to satisfy the response from the socks server, and then throwing away the excess bytes.  
This is a logic error, since those bytes may have come from the upstream server.

---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-20 12:35:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335233103  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  15 | #include <boost/asio/async_result.hpp>
  16 |- #include <string>
  16 |+ #include <boost/utility/string_view.hpp>
```

> Username: vinniefalco  
> Created_at: 2019-12-20 12:35:28 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360354577  

`<boost/beast/core/string_type.hpp>` instead


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-20 13:59:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335271051  

📁 example/contrib/socks-client/socks/impl/async_handshake_v5.hpp

```diff
  39 |         Handler, typename Stream::executor_type>>
  39 |- class socks5_op : public base_type
  40 |+ class socks5_op : public base_type, public net::coroutine
```

> Username: vinniefalco  
> Created_at: 2019-12-20 13:59:40 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360383345  

`boost::asio::coroutine`, because fauxroutines will certainly never make it into the stdlib :)

> Username: madmongo1  
> Created_at: 2019-12-20 17:35:13 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360484616  

doesn't this tie the implementation to boost asio?   
  
Should beast have its own fauxroutine?

> Username: vinniefalco  
> Created_at: 2019-12-22 13:21:53 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360705246  

Well, beast is tied to boost::asio at the moment, so there's no getting around that. I am very confident that Networking-TS / C++ will *never* get fauxroutines so the `net::` prefix is misleading here. This should read `boost::asio::coroutine`. Eventually we might incorporate it into some standalone thing but it can never be in net::.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-20 15:31:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335323504  

📁 example/contrib/socks-client/socks/impl/async_handshake_v4.hpp

```diff
  45 |+ struct socks4_op 
  46 |+ : base_type
  47 |+ , net::coroutine
```

> Username: vinniefalco  
> Created_at: 2019-12-20 15:31:53 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360422655  

`boost::asio::coroutine`


---

## Comment 26

> Username: madmongo1  
> Created_at: 2019-12-21 09:46:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#issuecomment-568167856  

@vinniefalco Having read the SOCKS 4 and 5 RFCs carefully, My view is that the SOCKS4 handshake handler should have an extra parameter of type `ip::tcp::endpoint&` in order to receive the endpoint chosen by the socks server at its client address when connecting to the downstream host.  
  
This data is returned by the socks server as part of the protocol.  
  
This will become particularly acute when imlementing the `BIND` method in socks4, where the socks server is acting as an an accepting host on behalf of the client. The client will need to know the external bound address in order to communitcate it to people who want to make inbound connections.  
  
The more I look at these protocols, the more I take the view that in ASIO terms, SOCKS is a stream layer, in the same way that SSL is a stream layer.

---

## Comment 27

> Username: madmongo1  
> Created_at: 2019-12-21 09:46:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#issuecomment-568167910  

For socks 5, the stream approach is unavoidable, for reasons I have posted in #beast in slack.

---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-21 12:41:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335603863  

📁 example/contrib/socks-client/socks/impl/async_handshake_v5.hpp

```diff
 410 |+   void operator()(
 411 |+     HandlerType&& handler,
 412 |+     AsyncStream& stream,
```

> Username: vinniefalco  
> Created_at: 2019-12-21 12:41:10 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360647316  

I'm not certain, but I believe references need special treatment. You might ask in #general . I think Chris passes them as pointers to avoid some ambiguity (although, this could be because he tries to be C++03 compatible).

> Username: madmongo1  
> Created_at: 2019-12-22 09:37:06 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360693689  

I had a look in the asio code. Chris performs a BOOST_ASIO_MOVE_ARG on each argument. In c++11 or better, this will of course ccast all arguments to r-values which is fine because the composed operation will cast them back to l-value references.  
In the obsolete pre-c++11 world there might be some boost-move-related issue, but this is no concern of ours.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-21 12:41:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335603876  

📁 example/contrib/socks-client/socks/impl/async_handshake_v5.hpp

```diff
 405 | };
 406 | 
 407 |+ struct async_handshake_v5_initiator
```

> Username: vinniefalco  
> Created_at: 2019-12-21 12:41:41 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360647328  

I think in the latest iteration, the initiating function object has a nested `executor_type` which enables some special thing.


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2019-12-21 12:42:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1783#pullrequestreview-335603928  

📁 example/contrib/socks-client/socks/impl/socks5_username_password_authentication.hpp

```diff
 113 |+     if (username.size() > 255 or password.size() > 255)
 114 |+     {
 115 |+       error = net::error::invalid_argument;
```

> Username: vinniefalco  
> Created_at: 2019-12-21 12:42:52 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360647390  

I would argue we need to throw `std::length_error` here

> Username: madmongo1  
> Created_at: 2019-12-22 09:40:39 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360693881  

When we have coroutines, maybe. Remember that this is a composed operation designed to be called from async code within an outer composed operation. The long username and password might have been legitimately passed in by a user who expected that GSSAPI authentication would happen, but instead the socks server mandated username/password. Adding exception handling in an asio fauxroutine would be a horror show.  
  
In any case, it's being hived off to a different branch for "later consideration"

> Username: vinniefalco  
> Created_at: 2019-12-22 13:48:27 UTC  
> Updated_at: 2019-12-22 16:36:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#discussion_r360706629  

Yeah that makes sense.


---

## Comment 31

> Username: stale[bot]  
> Created_at: 2020-01-21 19:57:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1783#issuecomment-576853746  

This issue has been open for a while with no activity, has it been resolved?

---
