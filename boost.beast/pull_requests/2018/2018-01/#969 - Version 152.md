# #969 Version 152 [Merged]

> Username: vinniefalco  
> Created at: 2018-01-05 18:27:38 UTC  
> Updated at: 2018-01-25 22:07:49 UTC  
> Merged at: 2018-01-12 20:28:48 UTC  
> Closed at: 2018-01-12 20:28:48 UTC  
> Url: https://github.com/boostorg/beast/pull/969  

* Refactor detect_ssl_op  
  
WebSocket:  
  
* Redistribute the read tests in the translation units  
* Refactor error headers  
* Add WebSocket error conditions  
  
API Changes:  
  
* Refactor WebSocket errors (API Change):  
  
Actions Required:  
  
* Code which explicitly compares error_code values against the  
  constant `websocket::error::handshake_failed` should compare  
  against `websocket::condition::handshake_failed` instead.  
  
* Code which explicitly compares error_code values against the  
  constant `websocket::error::failed` should compare  
  against `websocket::condition::protocol_violation` instead.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-05 21:26:22 UTC  
> Updated_at: 2018-01-12 19:54:51 UTC  
> Url: https://github.com/boostorg/beast/pull/969#issuecomment-355669615  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=h1) Report  
> Merging [#969](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6f08814a0c291eb9f03aaa1daefffdc45c1b9087?src=pr&el=desc) will **decrease** coverage by `0.08%`.  
> The diff coverage is `96.35%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/969/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #969      +/-   ##  
===========================================  
- Coverage    95.54%   95.45%   -0.09%       
===========================================  
  Files          105      106       +1       
  Lines        10543    10601      +58       
===========================================  
+ Hits         10073    10119      +46       
- Misses         470      482      +12  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.49% <100%> (-0.02%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/stream.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `99.76% <100%> (+0.01%)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/error.hpp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2Vycm9yLmhwcA==) | `100% <100%> (ø)` | |  
| [include/boost/beast/websocket/detail/frame.hpp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/rfc6455.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZmM2NDU1LmlwcA==) | `90% <100%> (-10%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `88.88% <88.67%> (-11.12%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `97.15% <96.15%> (-0.79%)` | :arrow_down: |  
| ... and [1 more](https://codecov.io/gh/boostorg/beast/pull/969/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=footer). Last update [6f08814...be14786](https://codecov.io/gh/boostorg/beast/pull/969?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: djarek  
> Created_at: 2018-01-06 14:06:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/969#pullrequestreview-87076102  

📁 include/boost/beast/websocket/error.hpp

```diff
  42 |+     /** The WebSocket operation caused a dynamic buffer overflow
  43 |+     */
  44 |     buffer_overflow,
```

> Username: djarek  
> Created_at: 2018-01-06 13:56:34 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160026265  

Would it make sense to address the duplication of buffer_overflow in both websocket and http errors?

> Username: TBBle  
> Created_at: 2018-01-08 09:16:43 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160097111  

Not in the error_code, they're separate categories, so duplication is likely and expected. It might make sense to have a single condition that can match both, if there's a use-case where you'd check that. You could also provide equivalence to `boost::system::errc::no_buffer_space` for example, if you don't want your own error_condition value.

> Username: djarek  
> Created_at: 2018-01-09 20:22:50 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160514976  

I think this came up some time ago when we were considering using internally `prepare_or_error`, however, the problem is that you can't have such a generic function for both websockets and http because of the duplication of that error code.

> Username: vinniefalco  
> Created_at: 2018-01-09 21:40:55 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160532586  

A common condition seems like a good idea.


📁 include/boost/beast/websocket/impl/error.ipp

```diff
  63 |-     }
  69 |+     default:
  70 |+     case error::closed:
```

> Username: djarek  
> Created_at: 2018-01-06 14:00:32 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160026315  

The fallthroughs here might generate a lot of warnings with `-Wimplicit-fallthrough` enabled.

> Username: TBBle  
> Created_at: 2018-01-08 08:41:07 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160091866  

`-Wimplicit-fallthrough` already generates a lot of noise for the existing codebase. I [proposed](https://github.com/boostorg/beast/pull/956/commits/1a3c8d2856ddfdca14c544a7bf860788be0489e2) adding `-Wno-implicit-fallthrough` to the CMake file, and I assumed it was already set in the Jamfile, for the examples. I'd suggest addressing that separately and globally.

> Username: vinniefalco  
> Created_at: 2018-01-09 21:35:17 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160531209  

That sounds to me like a defect in gcc or clang, these should not be producing warnings. Are you using a release version of the compiler? What version are you on where you see these warnings?

> Username: TBBle  
> Created_at: 2018-01-10 05:17:03 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160591321  

gcc-7.2.0 mingw-w64 build in msys. According to [the implementer of the warning](https://developers.redhat.com/blog/2017/03/10/wimplicit-fallthrough-in-gcc-7/), it's been implemented only now that we have `[[fallthrough]]` to annotate deliberate switch fallthrough, and it's part of `-Wextra` in gcc 7. It might be considered a defect that it's active with `-std=c++11`, I guess, but since gcc has attributes to indicate deliberate fallthrough on all C++ levels, I can see the rationale here.  
  
Assuming all target compilers correctly ignore unknown `[[]]` attributes (need to check this...), then the choice is either document that you need `-Wno-implicit-fallthrough` with gcc 7 or later, get gcc policy changed, or add `[[gnu::fallthrough]]` or some recognised comment to the falling-through lines.

> Username: vinniefalco  
> Created_at: 2018-01-10 12:59:42 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160669586  

I've already dealt with the fallthrough warnings throughout Beast by using the `BOOST_FALLTHROUGH` macro. But I don't think the following code should cause a warning:  
```  
switch(n)  
{  
case 0:  
case 1:  
    std::cout << "low\n";  
    break;  
default:  
    std::cout << "high\n";  
break;  
}  
```  
  
Or should it?

> Username: TBBle  
> Created_at: 2018-01-10 14:19:36 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160689017  

Why wouldn't it? It's implicitly falling through from `case 0` to `case 1`. Do you intend that because `case  0` labels the `case 1` statement, the warning should treat it as labelling the same statement that `case 1` labels, and hence not complain? It's not a bad case to make, although the counter-position that comes to mind is that if that labelling of a labelled statement was only because a macro had disappeared the statement `case 0` would have labelled, then it's still carrying the same risk for the reader as the more-obvious implicit fallthrough.  
  
i.e.  
```cpp  
switch(n)  
{  
case 0:  
#if _DEBUG  
    std::cout << "lowest\n";  
#endif  
case 1:  
    std::cout << "low\n";  
    break;  
default:  
    std::cout << "high\n";  
break;  
}  
```  
  
should probably warn in both `_DEBUG` and non-`_DEBUG` builds.

> Username: glenfe  
> Created_at: 2018-01-12 15:26:13 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r161250055  

This particular case should not generate any warnings:  
```cpp  
switch (n) {  
case 0:  
case 1:  
    /* code */  
}  
```  
I wouldn't use BOOST_FALLTHROUGH for these cases.

> Username: TBBle  
> Created_at: 2018-01-13 11:03:45 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r161369905  

I haven't checked if this construct is actually the cause of the surprise warnings in gcc 7, but if it is, then maybe worth raising with gcc? A quick look at the discussion in https://gcc.gnu.org/bugzilla/show_bug.cgi?id=7652 suggests it's not supposed to warn in this case ("empty body"), which is [apparently consistent with clang's behaviour](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=7652#c13)


---

## Review 3 [Commented]

> Username: TBBle  
> Created_at: 2018-01-08 09:10:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/969#pullrequestreview-87151049  

📁 include/boost/beast/websocket/error.hpp

```diff
 218 |+         Error codes with this value will compare equal to @ref condition::protocol_violation
 219 |+     */
 220 |+     bad_close_size,
```

> Username: TBBle  
> Created_at: 2018-01-08 09:10:52 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160096146  

Looking at this and the next, they appear to match some close_code values? My initial reaction is that `websocket::close_reason::code` should be returning a close_code enum, with its own category specifically for close_code. Then the error_condition could still compare equal for `condition::protocol_violation` on those codes, or any other close_codes that mean that. Users could also then tell if they have a valid close_reason from looking at the category of the error_code they received.

> Username: vinniefalco  
> Created_at: 2018-01-09 21:40:38 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160532531  

Close codes only represent a subset of the possible errors that can take place. And sometimes the value is not set correctly by the remote host. So the error code provides more concise information. I don't think it is a good idea to try to hook the close code system into a category. Especially because there can be custom close codes (for example in the 3,000 to 3,999 range) which are application specific and may have different meanings.

> Username: TBBle  
> Created_at: 2018-01-10 05:25:00 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160591964  

The "subset of possible errors" is why you'd have a category. The category decides how to deal with the interactions of that subset with the real world. Application specific close codes could easily work by deriving from a `ws_close_code_category`, and handling the 3XXX codes, and then upcalling for anything else.  
  
The idea of the category is that if you have different meanings for error numbers (i.e. close_code 7 != `boost::beast::websocket::error` 7, then having different categories lets the category author define the interoperation.  
  
That said, close codes (e.g. none) might fall into that somewhat grey area where "non-zero success and/or zero failure" is not clearly supported or disallowed, and there's an ongoing need to clarify this.  
  
I also see that these two error codes are not set based on the peer-provided close code, but determined locally, so my idea of replacing these is not right. The general idea that close_code fits in the error_code model however, I do think is valid, modulo the above paragraph.  
  
It's also possible this demonstrates my under-experience with Beast itself.

> Username: vinniefalco  
> Created_at: 2018-01-10 12:54:45 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160667994  

I think it is more subjective. For example there are folks who think that the error code system should be used to reflect HTTP status codes (e.g. 200 OK). I am not in that group. Also note that the close frame includes an arbitrary utf-8 encoded string (which can be zero length). This doesn't fit so easily into the error code system.

> Username: TBBle  
> Created_at: 2018-01-10 14:24:52 UTC  
> Updated_at: 2018-01-12 19:54:43 UTC  
> Url: https://github.com/boostorg/beast/pull/969#discussion_r160690440  

Indeed, I'm one of those folks. That was my point about "non-zero success", as that turns out to be one of the technical sticking points to letting the implementors decide that; specifically, the semantic of `error_code::operator bool` is not clear, and usage guidance in blog-posts conflicts over it (as well as whether HTTP status codes make sense in `error_code`).  
  
Lack of message/string/data support in `error_code` is also pretty clear, and various approaches show up in projects like Boost.outcome (gives you a pointer into a ring-buffer) or our local `error_code` usage (locally subclassed `std::error_code` with a string added, and we just accept that slicing to `std::error_code` will lose that detail), to carry those around. It seems that as far as the standardisation effort was concerned, that's what the `what` in `system_error` was for, and if you weren't going to put your `error_code` into `system_error` and throw it, carrying a specific string was just left off-the-table. FIlesystem TS similarly gives richer results when throwing `path_error` than when simply setting an `error_code`, so this is not a once-off choice.  
  
Anyway, this isn't really a discussion for here. `error_code` practice is still evolving, I know of at least two people who've indicated plans to write papers addressing issues like this, and I also have a (smaller) one planned, but far down my backlog.


---
