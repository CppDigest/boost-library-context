# #125 B16 (API Changes) [Merged]

> Username: vinniefalco  
> Created at: 2016-10-10 18:52:21 UTC  
> Updated at: 2017-07-25 17:44:17 UTC  
> Merged at: 2016-10-14 19:59:06 UTC  
> Closed at: 2016-10-14 19:59:06 UTC  
> Url: https://github.com/boostorg/beast/pull/125  

- Make value optional in param-list  
- Frame processing routines are member functions  
- Fix on_headers called twice from basic_parser_v1  
- Constrain parser_v1 constructor  
- Improve first line serialization  
- Add pause option to on_headers interface  
- Refactor base_parser_v1 callback traits:  
- Refine Parser concept  
- Relax ForwardIterator requirements in FieldSequence  
  
API Changes:  
- Rename mask_buffer_size to write_buffer_size  
- Make auto_fragment a boolean option

---

## Review 1 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-10 19:14:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-3557813  

📁 include/beast/http/detail/rfc7230.hpp

```diff
 244 |+             break;
 245 |+         ++it;
 246 |+     }
```

> Username: miguelportilla  
> Created_at: 2016-10-10 19:14:47 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82665915  

alternate suggestion:  
  
while(it != end && is_tchar(*it))  
    ++it;

> Username: nbougalis  
> Created_at: 2016-10-11 04:03:06 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82721590  

Consider adopting this suggestion. Performance is _likely_ to be better.

> Username: ximinez  
> Created_at: 2016-10-11 18:11:13 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82854594  

Why not:  
  
```  
for(; it != end && is_tchar(*it); ++it) {}  
```  
  
They're basically equivalent, but I think a `for` is moderately prettier.

> Username: vinniefalco  
> Created_at: 2016-10-14 14:11:25 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83429297  

I use `while` to avoid having an empty body. I'm feeling Mickey's solution - will fix.


---

## Comment 2

> Username: coveralls  
> Created_at: 2016-10-10 19:16:42 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252719208  

[![Coverage Status](https://coveralls.io/builds/8268451/badge)](https://coveralls.io/builds/8268451)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **e12ea86fa1824c3ad14decdac6f5938db2091a6b on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 3

> Username: codecov-io  
> Created_at: 2016-10-10 19:18:33 UTC  
> Updated_at: 2016-10-14 19:28:40 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252719659  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/125?src=pr) is 97.55% (diff: 93.50%)  
  
> Merging [#125](https://codecov.io/gh/vinniefalco/Beast/pull/125?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **0.49%**  
  
``` diff  
@@             master       #125   diff @@  
==========================================  
  Files            71         72     +1     
  Lines          4054       4091    +37     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           3975       3991    +16     
- Misses           79        100    +21     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [325f579...771c427](https://codecov.io/gh/vinniefalco/Beast/compare/325f579a1dabcb976a981018153ae4753b0a36d6...771c4277f865ca3e7b11bf6d30256292ae036e80?src=pr)

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-10-10 20:27:14 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252736716  

[![Coverage Status](https://coveralls.io/builds/8269363/badge)](https://coveralls.io/builds/8269363)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **37d818d1125c535d3e3599c9cb6e9220c97b44b3 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-10-10 23:58:10 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252776707  

The loss of code coverage is disappointing

---

## Review 6 [Changes requested]

> Username: nbougalis  
> Created_at: 2016-10-11 04:52:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-3609720  

Spotted a few typos. Left one comment for additional code coverage.

📁 doc/design.qbk

```diff
  25 |- * Let library users make the important decisions such as how to
  26 |-   allocate memory or how to leverage flow control.
  32 |+ * Leave important decisions to the user, such as
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:18:34 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82722499  

Reads weird. Consider:  
  
> Leave important decisions, such as memory allocation and flow control, to the user.

> Username: vinniefalco  
> Created_at: 2016-10-11 14:00:14 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82798466  

Will fix


📁 doc/master.qbk

```diff
  52 |-     An overview of features, requirements, and credits, plus
  53 |-     rationale and design information.
  52 |+     An introduction wsth features, requirements, and credits.
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:21:42 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82722650  

Typo.

> Username: vinniefalco  
> Created_at: 2016-10-11 14:00:17 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82798476  

Will fix


📁 doc/types/Parser.qbk

```diff
  32 |     [
  30 |-         Returns `true` when a complete HTTP/1 message has been parsed.
  33 |+         Returns `true` when the parser is complete.
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:23:09 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82722759  

"the parser" should be "parsing".

> Username: vinniefalco  
> Created_at: 2016-10-11 14:00:23 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82798491  

Will fix


📁 include/beast/http/basic_parser_v1.hpp

```diff
 188 | 
 161 |-     @li *2* The parser should skip ths body, this is an
 189 |+     @li `upgrade` The parser should skip ths body, this is an
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:26:05 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82722926  

Typo _ths_

> Username: nbougalis  
> Created_at: 2016-10-11 04:26:35 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82722951  

Also the comma would better as a semicolon.

> Username: vinniefalco  
> Created_at: 2016-10-11 14:00:27 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82798498  

Will fix


📁 include/beast/http/rfc7230.hpp

```diff
  21 |-     a semicolon.
  18 |+     This container allows iteration of the parameter list in a HTTP
  19 |+     extension. The parameter list is a series of name/value" pairs
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:33:26 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82723356  

Rogue "

> Username: vinniefalco  
> Created_at: 2016-10-14 14:12:26 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83429487  

will fix


📁 include/beast/websocket/impl/stream.ipp

```diff
 699 |+     if(compress)
 700 |     {
 825 |-         // send header and payload
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:42:11 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82723806  

Consider a comment to indicate the block is intentionally empty.

> Username: vinniefalco  
> Created_at: 2016-10-11 14:02:45 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82799014  

I can't find the code anymore

> Username: ximinez  
> Created_at: 2016-10-11 22:49:27 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82905770  

https://github.com/vinniefalco/Beast/blob/b16/include/beast/websocket/impl/stream.ipp#L699-L701

> Username: vinniefalco  
> Created_at: 2016-10-14 14:15:12 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83430016  

Will fix, good find.


📁 test/websocket/stream.cpp

```diff
 914 |+                 ws.set_option(auto_fragment{true});
 915 |+                 ws.set_option(write_buffer_size{8});
 916 |+                 ws.write(sbuf("Now is the time for all good men"));
```

> Username: nbougalis  
> Created_at: 2016-10-11 04:51:14 UTC  
> Updated_at: 2016-10-14 19:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82724251  

Consider also testing with messages that are 7, 8 and 9 bytes.

> Username: vinniefalco  
> Created_at: 2016-10-14 14:15:41 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83430108  

This is still a little bit in flux, I'm going to wait until I have finished the permessage-deflate extension and then revise all the tests, and try to get coverage up.

> Username: miguelportilla  
> Created_at: 2016-10-14 17:47:39 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83469264  

This loop breaks on the first iteration as is (n=199);

> Username: vinniefalco  
> Created_at: 2016-10-14 18:08:16 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83473236  

That's a major bug... those loops should start from 0.


---

## Comment 7

> Username: coveralls  
> Created_at: 2016-10-11 13:09:38 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252911743  

[![Coverage Status](https://coveralls.io/builds/8281375/badge)](https://coveralls.io/builds/8281375)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **c2a420e1a91827d27e975c474a02879592db8f33 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2016-10-11 14:06:05 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252926645  

Added "Cmake install and VS2015 support"

---

## Comment 9

> Username: coveralls  
> Created_at: 2016-10-11 14:18:13 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252930087  

[![Coverage Status](https://coveralls.io/builds/8282754/badge)](https://coveralls.io/builds/8282754)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **8b3117636d380b47dc9b308fad13d89f8b274788 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 10

> Username: coveralls  
> Created_at: 2016-10-11 14:30:38 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252933861  

[![Coverage Status](https://coveralls.io/builds/8282949/badge)](https://coveralls.io/builds/8282949)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **8b3117636d380b47dc9b308fad13d89f8b274788 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 11

> Username: coveralls  
> Created_at: 2016-10-11 15:14:04 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-252947279  

[![Coverage Status](https://coveralls.io/builds/8283586/badge)](https://coveralls.io/builds/8283586)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **7df642f3d1e5d173a7d547e31284ef78a94b56e2 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Review 12 [Changes requested]

> Username: ximinez  
> Created_at: 2016-10-13 23:23:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-3732504  

📁 include/beast/http/detail/rfc7230.hpp

```diff
 328 |         if(! detail::is_tchar(*it))
 329 |             break;
 330 |     }
```

> Username: ximinez  
> Created_at: 2016-10-11 20:44:33 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82884534  

Should this loop be replaced with a call to `skip_token`?

> Username: miguelportilla  
> Created_at: 2016-10-14 15:51:07 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83449941  

@ximinez  Good suggestion, as long as `it` is incremented before the call.  
  
Or  
  
while(++it != end && is_tchar(*it));

> Username: vinniefalco  
> Created_at: 2016-10-14 16:06:51 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83452767  

Are you saying to replace  
  
```  
    for(;;)  
    {  
        ++it;  
        if(it == end)  
            break;  
        if(! detail::is_tchar(*it))  
            break;  
    }  
```  
  
with  
  
```  
while(++it != end && detail::is_tchar(*it));  
```  
  
?

> Username: miguelportilla  
> Created_at: 2016-10-14 16:20:54 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83455146  

I am suggesting instead of the loop  
`skip_token(++it, end);`  
or  
`while(++it != end && detail::is_tchar(*it));`

> Username: vinniefalco  
> Created_at: 2016-10-14 16:29:50 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83456531  

`skip_token(++it, end);` looks good, will fix

---

📁 include/beast/http/detail/rfc7230.hpp

```diff
 379 |             if(! detail::is_tchar(*it))
 380 |                 break;
 381 |         }
```

> Username: ximinez  
> Created_at: 2016-10-11 20:48:20 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82885316  

Should this loop also be replaced with a call to `skip_token`?

> Username: miguelportilla  
> Created_at: 2016-10-14 16:22:54 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83455464  

Same as above. At the very least `it++` should be `++it`

> Username: vinniefalco  
> Created_at: 2016-10-14 16:29:56 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83456556  

Will fix


📁 doc/websocket.qbk

```diff
 311 |-     ws.set_option(beast::websocket::auto_fragment_size{8192});
 311 |+     ws.set_option(beast::websocket::auto_fragment{true};
 312 |+     ws.set_option(beast::websocket::write_buffer_size{16384};
```

> Username: ximinez  
> Created_at: 2016-10-11 22:32:24 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r82903472  

Missing the `)` on both these lines.

> Username: vinniefalco  
> Created_at: 2016-10-14 16:07:36 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83452863  

Will fix


📁 doc/design.qbk

```diff
 163 |     The Beast.HTTP message model is suitable for HTTP/2 and can be re-used.
```

> Username: ximinez  
> Created_at: 2016-10-12 18:08:18 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83064681  

I can't add the comment to the right line from here, but there's a double double quote on line 155  
`"What about HTTP/2?""`

---

📁 doc/design.qbk

```diff
 439 |     aggregation and memory reuse of memory. The implementation of
```

> Username: ximinez  
> Created_at: 2016-10-12 18:15:52 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83066354  

On line 375, which I can't comment on directly, you've got a backtick in the wrong place.  
`represented by a `std::function equivalent`.`  
Should be  
`represented by a `std::function` equivalent.`

---

📁 doc/design.qbk

```diff
 219 |-     [@http://www.boost.org/doc/libs/1_60_0/doc/html/boost_asio/reference/AsyncWriteStream.html AsyncWriteStream].
 277 |+     __AsyncReadStream__, __AsyncWriteStream__, __SyncReadStream__, __SyncWriteStream__.
 278 |+ 
```

> Username: ximinez  
> Created_at: 2016-10-12 18:26:03 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83068611  

Lines 282-283  
`A `beast::websocket::stream` is constructible and movable in a manner identical  
`to a boost::asio::ip::tcp::socket`.`  
Should be  
`A `beast::websocket::stream` is constructible and movable in a manner identical  
to a `boost::asio::ip::tcp::socket`.`


📁 doc/types/FieldSequence.qbk

```diff
  25 |     [
  26 |-         A type that meets the requirements of `Field`.
  26 |+         A type that meets the requirements of @b Field.
```

> Username: ximinez  
> Created_at: 2016-10-12 21:02:52 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83098026  

Is `@b` supposed to be a control code? It renders straight through on my end.   
![screenshot b](https://cloud.githubusercontent.com/assets/6757714/19327590/b1d202f4-909d-11e6-93fd-2fd14c9e80c2.png)

> Username: vinniefalco  
> Created_at: 2016-10-13 23:47:15 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83336461  

Should be `[*ForwardIterator]` to get bold, the `@b` is Javadoc markdown.

> Username: vinniefalco  
> Created_at: 2016-10-14 16:49:00 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83459585  

will fix


📁 doc/types/Parser.qbk

```diff
  23 |+ * `b` is a value meeting the requirements of __ConstBufferSequence__.
  24 | 
  25 | * `ec` is a value of type [link beast.ref.error_code `error_code&`].
```

> Username: ximinez  
> Created_at: 2016-10-12 21:07:21 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83098786  

Is the `&` correct here? It's passed to the function as a reference, but can be declared in the caller as a value.

> Username: vinniefalco  
> Created_at: 2016-10-13 23:47:46 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83336514  

It has to be a writable reference

> Username: vinniefalco  
> Created_at: 2016-10-14 16:49:51 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83459715  

I believe this is correct as written.


📁 doc/types/Reader.qbk

```diff
  27 |-     `std::is_same<decltype(m.body), Body::value_type>:value == true`
  28 |- 
  27 |+     `std::is_same<decltype(m.body), Body::value_type>::value == true`
```

> Username: ximinez  
> Created_at: 2016-10-12 21:18:38 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83100819  

As above, is the `&` correct here and in the description of `ec`? It's passed to the function as a reference, but can be declared in the caller as a value.

> Username: vinniefalco  
> Created_at: 2016-10-14 16:50:36 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83459833  

The intent is that the **Reader** can modify `ec` so it needs to be a modifiable reference. The caller can pass an lvalue if they want. But not an rvalue (temporaries can't bind to non-const references).  
  
I believe this is correct as written.


📁 include/beast/http/basic_parser_v1.hpp

```diff
 104 |+         The state of the parser is preserved; a subsequent
 105 |+         call to provide input data will resume parsing at the
 106 |+         beginning of the message body.
```

> Username: ximinez  
> Created_at: 2016-10-13 20:11:02 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83300895  

I don't see this paragraph in the rendered docs.

---

📁 include/beast/http/basic_parser_v1.hpp

```diff
 191 | 
 192 |+     @li `pause` The parser should preserve the parsing state and
 193 |+         return control to the caller.
```

> Username: ximinez  
> Created_at: 2016-10-13 20:34:54 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83305654  

There are enough differences between the descriptions here, and in the definition of `body_what` that I wonder if they shouldn't be reconciled into `body_what`, and have this whole list replaced by a link.

> Username: vinniefalco  
> Created_at: 2016-10-14 16:52:18 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83460112  

Will fix

---

📁 include/beast/http/basic_parser_v1.hpp

```diff
 188 | 
 189 |-     @li `upgrade` The parser should skip ths body, this is an
 189 |+     @li `upgrade` The parser should skip this body; its an
```

> Username: ximinez  
> Created_at: 2016-10-13 22:10:40 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83323169  

`it's`

> Username: vinniefalco  
> Created_at: 2016-10-14 16:51:52 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83460027  

Will fix


---

## Comment 13

> Username: coveralls  
> Created_at: 2016-10-13 23:39:07 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253670741  

[![Coverage Status](https://coveralls.io/builds/8330411/badge)](https://coveralls.io/builds/8330411)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **68045ef92fa99b0ea442b8b23345c8cc577d9ca3 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 14

> Username: coveralls  
> Created_at: 2016-10-14 14:29:24 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253816242  

[![Coverage Status](https://coveralls.io/builds/8339012/badge)](https://coveralls.io/builds/8339012)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **0a260be02c323e71d5895cf6c4b079a462aa10db on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 15

> Username: coveralls  
> Created_at: 2016-10-14 14:40:35 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253819348  

[![Coverage Status](https://coveralls.io/builds/8339227/badge)](https://coveralls.io/builds/8339227)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **0a260be02c323e71d5895cf6c4b079a462aa10db on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Review 16 [Commented]

> Username: nbougalis  
> Created_at: 2016-10-14 15:55:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4295891  

Looks good. Spotted a couple of of `@b` in here, should be easy to grep. Please address @ximinez's comments.

---

## Review 17 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 15:59:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4296749  

📁 include/beast/http/detail/rfc7230.hpp

```diff
 329 |     if(it == end)
 328 |-         return err();
 330 |+         return;
```

> Username: miguelportilla  
> Created_at: 2016-10-14 15:59:37 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83451529  

Suggestion, convert returns in this function  
from  
`return err();`  
to  
`{  
    err();  
    return;  
}`

> Username: vinniefalco  
> Created_at: 2016-10-14 16:00:46 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83451720  

Why would I do that?

> Username: miguelportilla  
> Created_at: 2016-10-14 16:09:09 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83453099  

Since this function returns void, I think it reads clearer.

> Username: vinniefalco  
> Created_at: 2016-10-14 16:12:07 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83453669  

`return;` means success, and `return err();` means an error occurred, do you feel that this is not accurately conveyed in the code? Or are you simply not used to seeing a `void` return type used in a `return` statement?

> Username: vinniefalco  
> Created_at: 2016-10-14 17:34:12 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83466831  

You should get used to `void` returns, they are already possible in the language and soon they will be treatable as first class types, see  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0146r1.html


---

## Review 18 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 16:45:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4303898  

📁 test/http/rfc7230.cpp

```diff
  46 |-             s.push_back('=');
  47 |-             s.append(str(p.second));
  46 |+             if(! p.second.empty())
```

> Username: miguelportilla  
> Created_at: 2016-10-14 16:45:56 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83459081  

Suggestion: It may be beneficial to call s.reserve before the loop.

> Username: vinniefalco  
> Created_at: 2016-10-14 17:03:36 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83462037  

I have a hard time getting excited about a microoptimization in unit test code...

> Username: miguelportilla  
> Created_at: 2016-10-14 17:17:21 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83464082  

Agreed! I forgot this was a test.


---

## Review 19 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 16:55:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4305389  

📁 include/beast/websocket/detail/stream_base.hpp

```diff
 193 |+             return err(close_code::protocol_error);
 194 |+         }
 195 |+         if(rd_fh_.rsv1 || rd_fh_.rsv2 || rd_fh_.rsv3)
```

> Username: miguelportilla  
> Created_at: 2016-10-14 16:55:26 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83460685  

slightly faster  
`if(b[0]&0x70)`

> Username: vinniefalco  
> Created_at: 2016-10-14 17:35:37 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83467082  

Hmm the current version is a bit more clear


---

## Review 20 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 17:00:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4306180  

📁 include/beast/websocket/detail/stream_base.hpp

```diff
 206 |+             return err(close_code::protocol_error);
 207 |+         }
 208 |+         if(rd_fh_.rsv1 || rd_fh_.rsv2 || rd_fh_.rsv3)
```

> Username: miguelportilla  
> Created_at: 2016-10-14 17:00:09 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83461497  

suggest: if(b[0]&0x70)

> Username: vinniefalco  
> Created_at: 2016-10-14 17:35:39 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83467092  

Hmm the current version is a bit more clear


---

## Review 21 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 17:00:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4306218  

📁 include/beast/websocket/detail/stream_base.hpp

```diff
 229 |+             return err(close_code::protocol_error);
 230 |+         }
 231 |+         if(rd_fh_.rsv1 || rd_fh_.rsv2 || rd_fh_.rsv3)
```

> Username: miguelportilla  
> Created_at: 2016-10-14 17:00:22 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83461537  

if(b[0]&0x70)

> Username: vinniefalco  
> Created_at: 2016-10-14 17:35:59 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83467158  

Hmm the current version is a bit more clear. Also, this line will change with permessage-deflate (which uses some of the now-reserved bits).


---

## Review 22 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 17:23:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4309602  

📁 include/beast/websocket/detail/mask.hpp

```diff
  70 |- using maskgen = maskgen_t<std::mt19937>;
  70 |+ // VFALCO NOTE This generator has 5KB of state!
  71 |+ //using maskgen = maskgen_t<std::mt19937>;
```

> Username: miguelportilla  
> Created_at: 2016-10-14 17:23:02 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83465009  

Super nit-pick: missing space


---

## Review 23 [Commented]

> Username: miguelportilla  
> Created_at: 2016-10-14 17:27:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/125#pullrequestreview-4310345  

📁 include/beast/websocket/impl/stream.ipp

```diff
 671 |+ else
 672 |+         write frame header, buffers as one frame
 673 |+ */
```

> Username: miguelportilla  
> Created_at: 2016-10-14 17:27:50 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83465786  

Was this comment intentionally left?

> Username: vinniefalco  
> Created_at: 2016-10-14 17:30:25 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83466190  

Yes those are implementation notes

> Username: vinniefalco  
> Created_at: 2016-10-14 17:36:59 UTC  
> Updated_at: 2016-10-14 19:04:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#discussion_r83467324  

Yes its part of some implementation notes


---

## Comment 24

> Username: vinniefalco  
> Created_at: 2016-10-14 17:38:59 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253869915  

Doc preview is here:  
http://vinniefalco.github.io/beast

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2016-10-14 17:39:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253869970  

Addressed all comments, the changes are individually marked

---

## Comment 26

> Username: miguelportilla  
> Created_at: 2016-10-14 18:11:50 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253879368  

overview.qbk misspells Christophers last name. should be Kohlhoff not Kohloff

---

## Comment 27

> Username: coveralls  
> Created_at: 2016-10-14 18:16:03 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253880590  

[![Coverage Status](https://coveralls.io/builds/8343037/badge)](https://coveralls.io/builds/8343037)  
  
Coverage decreased (-1.0%) to 97.067% when pulling **12e2c0e4a77ca424a0c89cede13b31b6d7855d4d on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 28

> Username: coveralls  
> Created_at: 2016-10-14 18:25:41 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253882982  

[![Coverage Status](https://coveralls.io/builds/8343214/badge)](https://coveralls.io/builds/8343214)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **12e2c0e4a77ca424a0c89cede13b31b6d7855d4d on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 29

> Username: coveralls  
> Created_at: 2016-10-14 18:36:59 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253885819  

[![Coverage Status](https://coveralls.io/builds/8343256/badge)](https://coveralls.io/builds/8343256)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **12e2c0e4a77ca424a0c89cede13b31b6d7855d4d on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 30

> Username: coveralls  
> Created_at: 2016-10-14 18:40:39 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253886699  

[![Coverage Status](https://coveralls.io/builds/8343434/badge)](https://coveralls.io/builds/8343434)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **88adc71eeb79a218783f50ec3f2f46e1159d57ac on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 31

> Username: coveralls  
> Created_at: 2016-10-14 18:49:28 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253888865  

[![Coverage Status](https://coveralls.io/builds/8343503/badge)](https://coveralls.io/builds/8343503)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **88adc71eeb79a218783f50ec3f2f46e1159d57ac on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 32

> Username: coveralls  
> Created_at: 2016-10-14 19:07:17 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253893848  

[![Coverage Status](https://coveralls.io/builds/8343815/badge)](https://coveralls.io/builds/8343815)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **88adc71eeb79a218783f50ec3f2f46e1159d57ac on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---

## Comment 33

> Username: coveralls  
> Created_at: 2016-10-14 19:28:27 UTC  
> Url: https://github.com/boostorg/beast/pull/125#issuecomment-253898544  

[![Coverage Status](https://coveralls.io/builds/8344157/badge)](https://coveralls.io/builds/8344157)  
  
Coverage decreased (-0.5%) to 97.556% when pulling **771c4277f865ca3e7b11bf6d30256292ae036e80 on b16** into **325f579a1dabcb976a981018153ae4753b0a36d6 on master**.

---
