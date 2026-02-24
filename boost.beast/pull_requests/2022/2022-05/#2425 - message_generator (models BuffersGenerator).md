# #2425 message_generator (models BuffersGenerator) [Closed]

> Username: sehe  
> Created at: 2022-05-13 03:26:04 UTC  
> Updated at: 2022-06-08 23:30:36 UTC  
> Closed at: 2022-05-22 08:19:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425  

This implements Issue #2421

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 03:31:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-971664311  

📁 example/http/server/stackless/http_server_stackless.cpp

```diff
 104 |+ template <class Body, class Allocator>
 105 |+ http::message_generator
 106 | handle_request(
```

> Username: vinniefalco  
> Created_at: 2022-05-13 03:31:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871971545  

Are all the `handle_request` functions in the examples still exactly the same?

> Username: sehe  
> Created_at: 2022-05-13 03:35:05 UTC  
> Updated_at: 2022-05-13 03:42:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871972708  

Didn't check, but fully expect it indeed. The reason I didn't check is because this realization seemed so blindingly obvious, that I was 1000% certain that this was on purpose and the priority was to make each flavour of server examples self-contained¹.  
  
However, now that type-erasure is in the mix, there's incentive to put it separately, as it might make a considerable dent in build times with a suitable `libbeast-http-server-logic` static target, or so.  
  
¹(modulo certificate business)

> Username: sehe  
> Created_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872782525  

Re-reading, you likely weren't talking about extracting a translation unit - Yes I applied only identical transformations to identical functions in various examples.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-05-13 03:34:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1125624697  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 03:40:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-971667966  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
  40 |+ template <class T>
  41 |+ struct is_buffers_generator
  42 |+     : integral_constant<bool, automatically_determined>
```

> Username: vinniefalco  
> Created_at: 2022-05-13 03:40:39 UTC  
> Updated_at: 2022-05-13 03:40:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871974519  

`automatically_determined` works?

> Username: sehe  
> Created_at: 2022-05-13 03:42:10 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871975051  

`#ifdef BOOST_BEAST_DOXYGEN` - so yeah. I copied that pattern from existing traits.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 03:41:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-971668464  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
 523 |         // Send the response
 617 |-         handle_request(*doc_root_, parser_->release(), queue_);
 524 |+         enqueue(handle_request(*doc_root_, parser_->release()));
```

> Username: vinniefalco  
> Created_at: 2022-05-13 03:41:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871974987  

Can't say I'm a fan of this name

> Username: sehe  
> Created_at: 2022-05-13 12:43:05 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872358178  

Can't say either. Can make it boring `do_write`. Problem is that it doesn't. It enqueues the write, and potentially starts the write loop. Better ideas?

> Username: vinniefalco  
> Created_at: 2022-05-17 14:41:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874908124  

append, push_back, send, maybe_send, add_response.  
  
`do_write` is also perfectly fine. `enqueue` is above the target reading comprehension level. Or how about `maybe_write`?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 03:43:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-971669096  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
 563 |+                                    &http_session::on_write,
 564 |+                                    derived().shared_from_this(),
 565 |+                                    not keep_alive));
```

> Username: vinniefalco  
> Created_at: 2022-05-13 03:43:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871975592  

we don't use the words for logical operators (`or`, `and`, etc) because 99% of people are not used to seeing it and it looks like an identifier. and the indent is too far

> Username: sehe  
> Created_at: 2022-05-13 12:46:53 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872361392  

I like your scientific sourcing :) My reason is that people read subliminally. Negations stand out in some places (`if (!cond)` e.g. is fine for me) but don't in others. E.g. 99% of people will subliminally read this:  
  
```c++  
 beast::bind_front_handler(  
                                   &http_session::on_write,  
                                   derived().shared_from_this(),  
                                   !keep_alive));  
```  
                                     
As "it binds a member a function with `keep_alive`", much like the & in `&http_session::on_write` is semantically noise.  
  
If it were up to me, I'd call the argument `keep_alive` and get rid of the negation there, so the code reads fine without negatives.

> Username: sehe  
> Created_at: 2022-05-13 15:02:49 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872497051  

Went ahead and did that refactor (accidentally straight to the PR branch...): https://github.com/sehe/beast/commit/ee332faaf976ea7289ade2074ad396ce8e459d06  
 If you don't veto, I'll squash it in


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-05-13 03:52:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1125630954  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-f551bf89.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:42:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972370931  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
   1 |+ [/
   2 |+     Copyright (c) 2016-2022 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:42:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872477992  

Shouldn't your name be here

> Username: sehe  
> Created_at: 2022-05-13 14:59:18 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872493594  

Should it? I just copy pasted some code :) I can add it

> Username: vinniefalco  
> Created_at: 2022-05-17 15:30:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874968017  

if you edited the file your name should be on it. if you created the file from scratch then ONLY your name should be on it.

> Username: sehe  
> Created_at: 2022-05-18 22:42:27 UTC  
> Updated_at: 2022-05-18 22:42:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876437903  

Done


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:43:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972371450  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
   8 |+ ]
   9 |+ 
  10 |+ [section:BuffersGenerator BuffersGenerator]
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:43:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872478337  

You don't need to repeat the section display name if it is the same as the identifier

> Username: sehe  
> Created_at: 2022-05-13 14:58:41 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872493014  

Ah. Quickbook lesson. I just copy pasted that from elsewhere :)

> Username: vinniefalco  
> Created_at: 2022-05-17 15:40:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874979492  

The place that you copied from is also wrong


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:43:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972372413  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  21 |+ concrete http message types.
  22 |+ 
  23 |+ Overloads of `write` and `async_write` operations are provided as free
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:43:54 UTC  
> Updated_at: 2022-05-13 14:43:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872479001  

You could put these in a table. That could be done in a future PR


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:45:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972374087  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  41 |+ 
  42 |+ * `G` denotes a type meeting the requirements of [*BuffersGenerator].
  43 |+ * `g` denotes a value of type `G`.
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:45:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872480186  

If an instance of _BuffersGenerator_ appears in code (such as in the refactored examples), I would use `g` as the variable name. Not `gen`, not `generator`, not `type_erased_message`, etc. I love single letter variables. I also love two character variables, but not quite as much as single letter variables. I don't love, but I do like three character variables. I think you see where I'm going with this.

> Username: sehe  
> Created_at: 2022-05-13 15:06:33 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872500303  

Yup. I actually [just](https://github.com/sehe/beast/commit/ee332faaf976ea7289ade2074ad396ce8e459d06) renamed the two left-over uses of `gen` out the examples.   
  
What about stuff like  
  
```c++  
async_write(AsyncWriteStream& stream,  
            BuffersGenerator generator,  
            CompletionToken&& token) ->  
```  
  
To me there's the value of consistency with other initiating functions. But only the "rote" async_compose/async_initiate wrappers, IYAM

> Username: sehe  
> Created_at: 2022-05-13 15:09:53 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872503356  

I think I'm happy to leave the library implementation files (buffers|message_generator.hpp) as is. Agree to mark this resolved?

> Username: vinniefalco  
> Created_at: 2022-05-17 14:21:49 UTC  
> Updated_at: 2022-05-17 14:21:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874882755  

Yes I often use the longer formal parameter names in declarations because it looks better in documentation. There's nothing wrong with having full names in the declaration, and then using single letters in the definition.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:46:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972376408  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  94 |+         behave as if `n` was equal to that number.
  95 |+ 
  96 |+         Any previously generated data that has not yet been `consume`d will
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:46:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872481833  

weird formatting bruh

> Username: sehe  
> Created_at: 2022-05-13 15:10:42 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872504053  

Fixed


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 14:47:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972377409  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
 112 |+     struct BuffersGenerator
 113 |+     {
 114 |+         using const_buffers_type = __implementation_defined__; /*<- no idea how to express this in a compiled snippet ->*/
```

> Username: vinniefalco  
> Created_at: 2022-05-13 14:47:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872482514  

you could put `net::const_buffer` there

> Username: sehe  
> Created_at: 2022-05-13 15:20:27 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872513388  

Okay. I wasn't sure whether "Exemplar" needed some kind of formal genericity. Done.

> Username: vinniefalco  
> Created_at: 2022-05-17 14:24:46 UTC  
> Updated_at: 2022-05-17 14:25:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874886470  

The Exemplar should compile (or, be as close to compile-able as possible), so that users can copy and paste it as a starting point.

> Username: vinniefalco  
> Created_at: 2022-05-19 01:33:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876508732  

I don't understand, it says resolved but it isn't changed?


---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-05-13 15:04:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1126155656  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 15:08:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972406095  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
 115 |-     class Send>
 116 |- void
 108 |+ // This function produces an HTTP response for the given request.
```

> Username: vinniefalco  
> Created_at: 2022-05-13 15:08:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872502014  

For functions which return meaningful values I start the brief with "Return...". For example:  
  
"Return a response for the given HTTP request."

> Username: sehe  
> Created_at: 2022-05-13 15:26:02 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872518387  

Done. It was literally already there. I won't post the git blame :)


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 15:11:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972410086  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
 553 |+         if(! response_queue_.empty())
 554 |+         {
 555 |+             http::message_generator msg =
```

> Username: vinniefalco  
> Created_at: 2022-05-13 15:11:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872505010  

`g`


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-05-13 15:21:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1126172306  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-a6a6cb8d.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-13 17:15:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-972563622  

📁 example/http/server/sync-ssl/http_server_sync_ssl.cpp

```diff
 246 |         // Determine if we should close the connection
 247 |-         bool close = not msg.keep_alive();
 247 |+         bool keep_alive = msg.keep_alive();
```

> Username: vinniefalco  
> Created_at: 2022-05-13 17:15:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r872615651  

Yes this is much better than `close`


---

## Review 18 [Commented]

> Username: sehe  
> Created_at: 2022-05-13 20:30:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-971666268  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
 112 |+     struct BuffersGenerator
 113 |+     {
 114 |+         using const_buffers_type = __implementation_defined__; /*<- no idea how to express this in a compiled snippet ->*/
```

> Username: sehe  
> Created_at: 2022-05-13 03:36:36 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871973149  

@vinniefalco for your review


📁 include/boost/beast/core/buffers_generator.hpp

```diff
 219 |+             BuffersGenerator>{ stream,
 220 |+                                std::move(generator) },
 221 |+         token, // REVIEW: why doesn't forwarding work here?
```

> Username: sehe  
> Created_at: 2022-05-13 03:38:07 UTC  
> Updated_at: 2022-05-13 20:30:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r871973632  

@vinniefalco for your review as well  
  
BTW Is it ok to put the write operations here? I mentioned this in the GH issue before - I don't know a better place off-hand

> Username: vinniefalco  
> Created_at: 2022-05-17 15:34:35 UTC  
> Updated_at: 2022-05-17 15:34:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874973255  

`<boost/beast/core/impl/buffers_generator.hpp>`

> Username: sehe  
> Created_at: 2022-05-18 23:30:10 UTC  
> Updated_at: 2022-05-18 23:30:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876458593  

Declarations remain here then. Okay, done.


---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-05-17 04:04:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1128384738  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-05-17 04:26:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1128396999  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-91bf2e5e.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:20:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975566066  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  28 |+ [heading Associated Types]
  29 |+ 
  30 |+ * [link beast.ref.boost__beast__core__is_buffers_generator `is_buffers_generator`]
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:20:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874880678  

The link to `is_buffers_generator` doesn't work, and it also missing from the Reference.

> Username: sehe  
> Created_at: 2022-05-18 23:51:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876467045  

Fixed


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:20:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975566731  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  35 |+ [warning
  36 |+     These requirements may undergo non-backward compatible
  37 |+     changes in subsequent versions.
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:20:35 UTC  
> Updated_at: 2022-05-17 14:20:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874881095  

Ah I would just leave this out

> Username: sehe  
> Created_at: 2022-05-18 23:52:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876467369  

Removed


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:22:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975570767  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  49 |+ [
  50 |+     [`G::const_buffers_type`]
  51 |+     [`implementation-defined`]
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:22:32 UTC  
> Updated_at: 2022-05-17 14:22:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874883655  

You don't need to say implementation-defined here, you should just leave the column empty because we are already naming a type.  
  
And if you were going to say _implementation-defined_ it should be in italics not a code font.

> Username: sehe  
> Created_at: 2022-05-18 23:53:13 UTC  
> Updated_at: 2022-05-18 23:53:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876467843  

Got it


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:23:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975572277  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  75 |+         generator is considered to have completed.
  76 |+ 
  77 |+         The function will ensure that `!ec` is `true` if there was
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:23:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874884682  

Peter prefers `ec.failed()` over `! operator!()`.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:24:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975573851  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  98 |+     ]
  99 |+ ][
 100 |+     [`is_buffers_generator<G>`]
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:24:13 UTC  
> Updated_at: 2022-05-17 14:24:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874885792  

This could be a link I suppose. Do I link them in the other concepts?

> Username: sehe  
> Created_at: 2022-05-18 23:56:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876468993  

Other concepts don't reflexively include their own trait. I think I got that from an Asio concept. Keep it or drop it?

> Username: vinniefalco  
> Created_at: 2022-05-19 01:32:05 UTC  
> Updated_at: 2022-05-19 01:32:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876508264  

I'm not sure... it does look nice in the list. Doesn't _have_ to be a link. Up to you.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:25:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975576369  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
 124 |+ [heading Models]
 125 |+ 
 126 |+ * [link beast.ref.boost__beast__http__message_generator `message_generator`]
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:25:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874887461  

This needs the namespace qualifier: `http::message_generator`


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:32:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975589408  

📁 doc/qbk/07_concepts/BuffersGenerator.qbk

```diff
  18 |+ 
  19 |+ In this way, serializers can be adapted as [*BuffersGenerator], for example
  20 |+ __message_generator__ which provides a type-erased interface for a variety of
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:32:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874896925  

`__message_generator__` is not rendering in the docs


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:42:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975606880  

📁 example/advanced/server-flex/advanced_server_flex.cpp

```diff
 539 |+         // loop
 540 |+         if (response_queue_.size() == 1)
 541 |+             do_write_loop();
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:42:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874908741  

can we change this to just `do_write` for simplicity

> Username: sehe  
> Created_at: 2022-05-18 23:59:23 UTC  
> Updated_at: 2022-05-18 23:59:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876470130  

`do_write` exists. That is, because you want me to rename `enqueue` to `do_write`. What about `do_write`/`queue_write`?

> Username: vinniefalco  
> Created_at: 2022-05-19 01:26:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876506351  

`queue_write` yes

> Username: sehe  
> Created_at: 2022-05-19 01:29:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876507241  

Doing the thing


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 14:55:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975633495  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
  74 |+ {
  75 |+     write_buffers_generator_op(AsyncWriteStream& stream,
  76 |+                                BuffersGenerator generator)
```

> Username: vinniefalco  
> Created_at: 2022-05-17 14:55:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874926809  

derp formatting

> Username: sehe  
> Created_at: 2022-05-19 00:00:46 UTC  
> Updated_at: 2022-05-19 00:00:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876470642  

```c++  
    write_buffers_generator_op(  
        AsyncWriteStream& s, BuffersGenerator g)  
        : s_(s), g_(std::move(g))  
    {  
    }  
```

> Username: vinniefalco  
> Created_at: 2022-05-19 01:27:39 UTC  
> Updated_at: 2022-05-19 01:27:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876506740  

This is better but this is better yet:  
```  
    write_buffers_generator_op(  
        AsyncWriteStream& s, BuffersGenerator g)  
        : s_(s)  
        , g_(std::move(g))  
    {  
    }  
```  
  
For two reasons, because it is easier to get a breakpoint, and because coverage will consider the two ctor-initializers to be separate lines.

> Username: sehe  
> Created_at: 2022-05-19 01:32:38 UTC  
> Updated_at: 2022-05-19 01:32:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876508468  

The best news for you is that not only do I remember, but it is now automatic whenever I format. :+1:


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:13:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975663869  

📁 test/beast/core/file_test.hpp

```diff
  89 | 
  90 |     auto const create =
  91 |-         [](fs::path const& path, std::string const& data = "")
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:13:16 UTC  
> Updated_at: 2022-05-17 15:14:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874948568  

in theory this change should be in a separate commit. C++11 does support default arguments in lambda parameter lists though, doesn't it?

> Username: sehe  
> Created_at: 2022-05-19 00:07:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876473420  

It clearly doesn't according to the diagnostic.  
  
And it in practice it already is its own commit (https://github.com/sehe/beast/commit/4ff33c8d27bc69a4d7aac394a1a3d801a0a86a33). Maybe you mean separate PR, in which case, ... could do, but feels a tad bureaucrat

> Username: vinniefalco  
> Created_at: 2022-05-19 01:30:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876507591  

Oh, yes it is already in its own commit (good). But I don't agree that C++11 does not support default arguments in a lambda parameter list. Can you please show a link on CI where this diagnostic is being emitted?


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975666068  

📁 test/beast/http/message_generator.cpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2022 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874950238  

shouldn't this be your name

> Username: sehe  
> Created_at: 2022-05-19 00:07:18 UTC  
> Updated_at: 2022-05-19 00:07:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876473504  

fixed


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975666275  

📁 test/beast/http/message_generator.cpp

```diff
  29 |+ namespace http {
  30 |+ 
  31 |+ namespace test_detail {
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874950385  

yuuuuck

> Username: sehe  
> Created_at: 2022-05-19 00:08:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876474007  

s/test_//g


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975666777  

📁 test/beast/http/message_generator.cpp

```diff
  80 |+ 
  81 |+ template<>
  82 |+ struct detail::is_body_sized<test_detail::fragmented_test_body>
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:14:52 UTC  
> Updated_at: 2022-05-17 15:14:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874950714  

huh, what's this `is_body_sized`?

> Username: sehe  
> Created_at: 2022-05-19 00:09:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876474250  

That's required to participate in the `prepare_payload` dance to set the `Content-Length`

> Username: vinniefalco  
> Created_at: 2022-05-19 01:30:37 UTC  
> Updated_at: 2022-05-19 01:30:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876507732  

\facepalm


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:32:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975695539  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
  70 |+     class AsyncWriteStream,
  71 |+     class BuffersGenerator>
  72 |+ struct write_buffers_generator_op
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:32:58 UTC  
> Updated_at: 2022-05-17 15:32:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874971379  

This needs to go in `<boost/beast/core/impl/buffers_generator.hpp>` so we don't force the doc toolchain to parse it needlessly.

> Username: sehe  
> Created_at: 2022-05-19 00:09:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876474415  

Already there now (with the earlier comment)


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:33:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975695780  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
 131 |+     >
 132 |+ size_t
 133 |+ write(SyncWriteStream& stream,
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:33:06 UTC  
> Updated_at: 2022-05-17 15:34:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874971564  

This needs to go in `<boost/beast/core/impl/buffers_generator.hpp>` so we don't force the doc toolchain to parse it needlessly.  
  
  
There should still be the declaration here though, and it needs a javadoc

> Username: sehe  
> Created_at: 2022-05-19 01:33:37 UTC  
> Updated_at: 2022-05-19 01:33:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876508834  

Just checked the brand new javadoc. Trying to find out whether I can @ref link concepts (probably not?)

> Username: vinniefalco  
> Created_at: 2022-05-19 02:14:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876523887  

You can add _BuffersGenerator_ as a concept to the docs and to the docca config:  
https://github.com/boostorg/beast/blob/9d23bec2bc523223a377582302bd28d04cb514ed/doc/xsl/custom-overrides.xsl#L18

> Username: sehe  
> Created_at: 2022-05-19 02:24:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876527892  

Oh shiiiiiiiny. What do you say I create a separate issue to improve the docs here, because there's some loose ends (we could use a "Generator oriented streaming operations" section/page, with that table you alluded to earlier).


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:34:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975697673  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
 174 |+     >
 175 |+ std::size_t
 176 |+ write(SyncWriteStream& stream, BuffersGenerator&& generator)
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:34:16 UTC  
> Updated_at: 2022-05-17 15:34:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874972920  

This needs to go in `<boost/beast/core/impl/buffers_generator.hpp>` so we don't force the doc toolchain to parse it needlessly.

> Username: sehe  
> Created_at: 2022-05-19 02:24:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876527940  

Done


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:34:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975697837  

📁 include/boost/beast/core/buffers_generator.hpp

```diff
 202 |+     >
 203 |+ auto
 204 |+ async_write(AsyncWriteStream& stream,
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:34:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874973043  

This needs to go in `<boost/beast/core/impl/buffers_generator.hpp>` so we don't force the doc toolchain to parse it needlessly.


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:36:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975700845  

📁 include/boost/beast/http/message_generator.hpp

```diff
  27 |+     message_generator(http::message<isRequest, Body, Fields>&&);
  28 |+ 
  29 |+     const_buffers_type prepare(error_code& ec)   { return impl_->prepare(ec);      }
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:36:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874975265  

Please always put braces on their own line:  
```  
const_buffers_type prepare(error_code& ec)  
{  
  return impl_->prepare(ec);  
}  
```  
  
Otherwise, it is harder to set breakpoints, and the coverage reports will have less granularity.

> Username: sehe  
> Created_at: 2022-05-19 00:15:15 UTC  
> Updated_at: 2022-05-19 00:15:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876476346  

Good point about coverage.


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:36:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975701781  

📁 include/boost/beast/http/message_generator.hpp

```diff
  48 |+ } // namespace http
  49 |+ } // namespace beast
  50 |+ } // namespace boost
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:36:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874975933  

I don't bother putting the word namespace there, as it adds 0 bits of information. But if it reassures you to keep it, then by all means go for it :)

> Username: sehe  
> Created_at: 2022-05-19 00:16:05 UTC  
> Updated_at: 2022-05-19 00:16:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876476622  

It's literally automatic for me. No religion


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2022-05-17 15:37:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2425#pullrequestreview-975703132  

📁 include/boost/beast/http/message_generator.hpp

```diff
  52 |+ #include <boost/beast/http/impl/message_generator.hpp>
  53 |+ 
  54 |+ #endif // BOOST_BEAST_HTTP_MESSAGE_GENERATOR_HPP
```

> Username: vinniefalco  
> Created_at: 2022-05-17 15:37:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r874976725  

Same here. Repeating the macro for the last endif, yields precisely 0 bits of information. And in fact it can yield negative information if the filename is changed and someone forgets to edit the comment at the bottom. But as before, if you feel reassured then keep it.

> Username: sehe  
> Created_at: 2022-05-19 00:20:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876478021  

I use #pragma once to not deal with that shit.  
  
But there's some arguments to keep the oldfashioned stuff around. I'm from the "all or nothing" school of thought here.  
  
Removing anyways because if others don't expect it, then it will lead to rot.

> Username: vinniefalco  
> Created_at: 2022-05-19 02:14:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#discussion_r876524137  

pragma once can't tell if you a header was included. so in practice you end up using both pragma once AND defining a macro... lol


---

## Comment 41

> Username: vinniefalco  
> Created_at: 2022-05-17 15:38:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1129024065  

Remaining:  
  
1. CI failures  
2. Overzealous indentation  
3. Refactoring/merging the commits

---

## Comment 42

> Username: sehe  
> Created_at: 2022-05-19 02:04:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1130996303  

> Remaining:  
>   
> 1. CI failures  
  
It was an new compiler-ism used in the test  
  
> 2. Overzealous indentation  
  
Unless mentioned above, I'm pretty sure I don't know all you're getting at here. There's a lot of contradictory forces. I try to keep in line with surrounding code, which in fact means that I'm manually reducing the max line width for certain files, not others. It's a balancing act for me.  
  
I'm very keen on getting consistency, if only for the fact that allows me to tune clang-format to Just Do It for me instead.  
  
> 3. Refactoring/merging the commits  
  
Doing that now.

---

## Comment 43

> Username: vinniefalco  
> Created_at: 2022-05-19 02:16:00 UTC  
> Updated_at: 2022-05-19 02:16:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131013930  

> Overzealous indentation  
  
This was mostly fixed but what I mean by this is when a subsequent line is indented by more than a tab stop. For example:  
```  
void really_really_really_really_really_really_really_really_long_function( int x,  
                                                                            int y);  
```

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2022-05-19 02:29:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131033560  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2022-05-19 02:34:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131041086  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2022-05-19 02:47:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131060589  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-726b1bf0.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2022-05-19 03:04:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131086678  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-df81aa8e.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2022-05-19 10:49:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131537062  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2022-05-19 11:07:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131552335  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-1720313a.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 50

> Username: sehe  
> Created_at: 2022-05-19 12:29:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131625336  

> An automated preview of the documentation is available at https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html  
  
Forgot to pull out the alphabetical order change to quickref.xml. Okay to leave as is?

---

## Comment 51

> Username: sehe  
> Created_at: 2022-05-19 12:34:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131629545  

> > Overzealous indentation  
>   
> This was mostly fixed but what I mean by this is when a subsequent line is indented by more than a tab stop. For example:  
>   
> ```  
> void really_really_really_really_really_really_really_really_long_function( int x,  
>                                                                             int y);  
> ```  
  
Good. I believe I must have got those out. More importantly, it's now automatic in the future.

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2022-05-19 15:11:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131851836  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2022-05-19 15:31:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131875947  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-9304f216.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 54

> Username: codecov[bot]  
> Created_at: 2022-05-19 15:59:02 UTC  
> Updated_at: 2022-05-20 12:59:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1131903989  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2425](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cccecf6) into [develop](https://codecov.io/gh/boostorg/beast/commit/9d23bec2bc523223a377582302bd28d04cb514ed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9d23bec) will **decrease** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2425/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2425      +/-   ##  
===========================================  
- Coverage    94.69%   94.67%   -0.03%       
===========================================  
  Files          149      151       +2       
  Lines        11773    11816      +43       
===========================================  
+ Hits         11149    11187      +38       
- Misses         624      629       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/beast/http/impl/message\_generator.hpp](https://codecov.io/gh/boostorg/beast/pull/2425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZV9nZW5lcmF0b3IuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/message\_generator.hpp](https://codecov.io/gh/boostorg/beast/pull/2425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL21lc3NhZ2VfZ2VuZXJhdG9yLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/serializer.hpp](https://codecov.io/gh/boostorg/beast/pull/2425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `90.90% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.22% <0.00%> (-0.19%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9d23bec...cccecf6](https://codecov.io/gh/boostorg/beast/pull/2425?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2022-05-19 19:11:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132100873  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2022-05-19 19:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132115949  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-825baaa6.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 57

> Username: sehe  
> Created_at: 2022-05-20 05:10:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132479298  

I can see us do some doc improvements in the future, but separately. They're not fundamentally related.

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2022-05-20 05:11:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132480258  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2022-05-20 05:21:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132484982  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2022-05-20 05:26:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132487833  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-826a90f0.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2022-05-20 05:44:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132497503  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-4e83f72b.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 62

> Username: sehe  
> Created_at: 2022-05-20 12:06:16 UTC  
> Updated_at: 2022-05-20 12:06:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132822269  

Removed `buffer_sequence_empty` since it was no longer required

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2022-05-20 12:11:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132830176  

An automated preview of the documentation is available at [https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2425.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2022-05-20 12:32:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1132847848  

![pullrequest](https://2425.beast.tracing.cppalliance.org/pullrequest-0d27da07.png)  
Timeline tracing charts: [https://2425.beast.tracing.cppalliance.org/index.html](https://2425.beast.tracing.cppalliance.org/index.html)

---

## Comment 65

> Username: sehe  
> Created_at: 2022-05-22 08:19:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2425#issuecomment-1133842878  

Merged via #2428

---
