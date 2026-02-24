# #1883 Update Release Notes [Merged]

> Username: madmongo1  
> Created at: 2020-03-23 14:51:40 UTC  
> Updated at: 2020-03-29 22:34:55 UTC  
> Merged at: 2020-03-29 22:34:55 UTC  
> Closed at: 2020-03-29 22:34:55 UTC  
> Url: https://github.com/boostorg/beast/pull/1883  

For comment and reference

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-23 16:29:44 UTC  
> Updated_at: 2020-03-28 07:22:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#issuecomment-602711101  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=h1) Report  
> Merging [#1883](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6b8b54f7612842cdff707d6bb27a87aef690598b&el=desc) will **not change** coverage by `%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1883/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1883   +/-   ##  
========================================  
  Coverage    95.12%   95.12%             
========================================  
  Files          156      156             
  Lines        12032    12032             
========================================  
  Hits         11446    11446             
  Misses         586      586             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=footer). Last update [6b8b54f...45e8771](https://codecov.io/gh/boostorg/beast/pull/1883?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-23 18:18:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-379700395  

📁 doc/qbk/release_notes.qbk

```diff
  18 |+ * Nested const and mutable buffer types for all
  19 |+   Beast dynamic buffers are refactored. Affected types:
  20 |+   - buffers_adapter
```

> Username: vinniefalco  
> Created_at: 2020-03-23 18:18:23 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r396660370  

need backticks for identifiers, e.g. `buffers_adaptor` (note spelling on this one)


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-23 18:18:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-379700681  

📁 doc/qbk/release_notes.qbk

```diff
  35 |+ 
  36 |+ * Refactor websocket read
  37 |+ * Refactor multi_buffer
```

> Username: vinniefalco  
> Created_at: 2020-03-23 18:18:45 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r396660661  

Users do not see any effect from a refactoring, thus we should exclude it from the release notes.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-380562448  

📁 doc/qbk/release_notes.qbk

```diff
  16 |+ [*API Changes]
  17 |+ 
  18 |+ * Nested mutable_data_type in Beast dynamic buffers is deprecated. Affected types:
```

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:05 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397352776  

`mutable_data_type`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-380562619  

📁 doc/qbk/release_notes.qbk

```diff
  36 |+ 
  37 |+ * Fix Content-Length parsing
  38 |+ * Correct buffer_bytes documentation
```

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:18 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397352920  

`buffer_bytes`


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-380563009  

📁 doc/qbk/release_notes.qbk

```diff
  59 |+ * file_stdio supports unicode paths
  60 |+ * Fix echo-op test
  61 |+ * file_win32 bodies respect http::serializer::split
```

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:45 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397353291  

backticks


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-380563119  

📁 doc/qbk/release_notes.qbk

```diff
  52 |+ * Clarify end-of-file behaviour in File::read docs
  53 |+ * file_body returns short_read on eof during read
  54 |+ * Fix bug in win32 file_body
```

> Username: vinniefalco  
> Created_at: 2020-03-24 17:56:54 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397353378  

`file_body` or `file_body_win32`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:23:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381096582  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 148 |+         {
 149 |+             // tokenize the Sec-Websocket-Protocol header offered by the client
 150 |+             http::token_list offered { req[http::field::sec_websocket_protocol] };
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:23:07 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397812744  

why braces


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:25:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381098105  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 154 |+ 
 155 |+ The Websocket protocol understands the concept of sub-protocols. If the client
 156 |+ is requesting one of a set of sub-protocols it will set the header `Sec-Websocket-Protocol`
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:25:28 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397813985  

It is spelled **Sec-WebSocket-Protocol** note capitalization. And it does not use a monospaced font, since it is not a C++ identifier.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:26:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381098569  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 151 | [code_websocket_2_5]
 152 | 
 153 |+ [heading Sub Protocols]
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:26:10 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397814362  

"Subprotocols" is one word:  
https://tools.ietf.org/html/rfc6455#section-1.9


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:27:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381099211  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 155 |+ The Websocket protocol understands the concept of sub-protocols. If the client
 156 |+ is requesting one of a set of sub-protocols it will set the header `Sec-Websocket-Protocol`
 157 |+ in the initial HTTP request. It is up to the server to parse the header and select one of the
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:27:11 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397814876  

in the initial WebSocket Upgrade HTTP request


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:28:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381100067  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 183 |+             // none of our supported protocols were offered
 184 |+ 
 185 |+             http::response<http::string_body> resp;
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:28:25 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397815544  

`res`


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:30:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381101467  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 159 |+ header in the accept header.
 160 |+ 
 161 |+ This is accomplished by means of a [link beast.ref.boost__beast__websocket__stream_base__decorator `decorator`]:
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:30:26 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397816638  

You need to explain what the code does. For example:  
  
"The code that follows demonstrates how a server reads an HTTP request, identifies it as a WebSocket Upgrade, and then checks for a matching subprotocol before performing the WebSocket handshake."


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:31:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381101987  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 181 |+             }
 182 |+ 
 183 |+             // none of our supported protocols were offered
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:31:14 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397817049  

There's a bug here, you call `ws.accept` and then you try to send an HTTP response

> Username: madmongo1  
> Created_at: 2020-03-25 13:15:45 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397843453  

yup, saw it. fixed


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 12:33:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381103470  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 159 |+                 };
 160 |+ 
 161 |+             for (auto proto : supported)
```

> Username: vinniefalco  
> Created_at: 2020-03-25 12:33:28 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397818289  

I would factor out the subprotocol checks into its own function with this signature:  
```  
string_view  
select_subprotocol( string_view subprotocols );  
```  
  
This could be a lambda if you want. This will clean up the logic, because calling `ws.accept` inside the `for` loop is clumsy and error-prone.

> Username: madmongo1  
> Created_at: 2020-03-25 13:16:08 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397843694  

wow!


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 13:48:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381162595  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 153 |+ [heading Subprotocols]
 154 |+ 
 155 |+ The Websocket protocol understands the concept of sub-protocols. If the client
```

> Username: vinniefalco  
> Created_at: 2020-03-25 13:48:22 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397866574  

"sub-protocols" is not hyphenated, check the RFC

> Username: vinniefalco  
> Created_at: 2020-03-25 13:48:37 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397866764  

WebSocket is always camel cased


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 13:50:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381164152  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 152 |+             };
 153 |+ 
 154 |+             std::string result;
```

> Username: vinniefalco  
> Created_at: 2020-03-25 13:50:02 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397867880  

Why `std::string` instead of `string_view`?

> Username: vinniefalco  
> Created_at: 2020-03-25 14:03:21 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397878205  

Is this because it has to be bound into the decorator? You really need to add comments explaining this. If there was ever a need for comment, this is an obvious case of it.

> Username: madmongo1  
> Created_at: 2020-03-26 15:42:35 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r398675391  

because the passing copies of string_views around seems like a really bad idea


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 13:51:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381165846  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 179 |+         if(websocket::is_upgrade(req))
 180 |+         {
 181 |+             std::string selected_protocol =
```

> Username: vinniefalco  
> Created_at: 2020-03-25 13:51:49 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397869250  

why not just  
```  
string_view subprotocol = select_subprotocol( req[http::field::sec_websocket_protocol] );  
```

> Username: madmongo1  
> Created_at: 2020-03-27 19:52:32 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399505102  

see above

> Username: vinniefalco  
> Created_at: 2020-03-27 19:58:26 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399507971  

Why not  
```  
std::string subprotocol = select_subprotocol( req[http::field::sec_websocket_protocol] );  
```  
then?

> Username: madmongo1  
> Created_at: 2020-03-27 22:46:38 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399571444  

my thinking was that the later line:  
```  
if (protocol.empty())  
```  
  
would not read as cleanly as:  
  
```  
if (selected_protocol.empty())  
```


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 13:54:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381168511  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 156 |+             for (auto proto : supported)
 157 |+             {
 158 |+                 auto iter = std::find(offered.begin(), offered.end(), proto);
```

> Username: vinniefalco  
> Created_at: 2020-03-25 13:54:44 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397871461  

If subprotocol matching is case-insensitive you could use this function:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/rfc7230.hpp#L280  
  
You have to find out if case matters though

> Username: madmongo1  
> Created_at: 2020-03-27 19:52:58 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399505310  

it's absolutely down to the implementor of the subprotocol


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-25 13:56:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-381170183  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 202 |+                 ws.set_option(
 203 |+                     stream_base::decorator(
 204 |+                         [selected_protocol](http::response_header<> &hdr) {
```

> Username: vinniefalco  
> Created_at: 2020-03-25 13:56:30 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r397872840  

This is rather annoying...A copy of `selected_protocol` exists for the lifetime of the `stream` (or until the decorator is changed). This isn't your problem but it does point out a design flaw.

> Username: madmongo1  
> Created_at: 2020-03-27 19:54:11 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399505926  

probably doesn't need to be a string in the sync version. But I'd rather have a string here otherwise I'm going to get a bunch of support emails from people using string_view objects in async initiators.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-27 19:59:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-383162401  

📁 doc/qbk/06_websocket/02_handshaking.qbk

```diff
 156 |+ is requesting one of a set of subprotocols it will set the header
 157 |+ [@https://tools.ietf.org/html/rfc6455#section-11.3.4 Sec-WebSocket-Protocol]
 158 |+ in the initial WebSocket Upgrade HTTP request. It is up to the server to parse the header and select one of the
```

> Username: vinniefalco  
> Created_at: 2020-03-27 19:59:06 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399508315  

overlong line, but not a showstopper


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-27 19:59:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-383162714  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 138 |+ 
 139 |+         // a function to select the most preferred protocol from a comma-separated list
 140 |+         auto best_protocol = [](string_view offered_tokens) -> std::string
```

> Username: vinniefalco  
> Created_at: 2020-03-27 19:59:39 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399508581  

Prefer `select_subprotocol` over `best_protocol`


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-27 20:00:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1883#pullrequestreview-383163161  

📁 test/doc/websocket_2_handshaking.cpp

```diff
 181 |+             // we store the selected protocol in a std::string here
 182 |+             // because we intend to capture it in the decorator's lambda
 183 |+             // below
```

> Username: vinniefalco  
> Created_at: 2020-03-27 20:00:23 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399508903  

widow

> Username: madmongo1  
> Created_at: 2020-03-27 22:51:00 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399572628  

Perhaps I misunderstood. I put this comment in because I thought you were asking for exposition of why the protocol was being captured in a string rather than a string_view

> Username: vinniefalco  
> Created_at: 2020-03-27 22:56:55 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399574234  

What I mean is that the word "below" is all by iteself

> Username: madmongo1  
> Created_at: 2020-03-27 23:04:43 UTC  
> Updated_at: 2020-03-27 23:09:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1883#discussion_r399576173  

Oh I see. I was trying to avoid crossing the undefined column boundary.


---
