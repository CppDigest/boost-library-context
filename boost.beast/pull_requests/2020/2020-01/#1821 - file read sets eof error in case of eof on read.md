# #1821 file read sets eof error in case of eof on read: [Closed]

> Username: madmongo1  
> Created at: 2020-01-24 11:44:18 UTC  
> Updated at: 2020-01-28 16:48:33 UTC  
> Closed at: 2020-01-28 16:48:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1821  

fixes #1818

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-01-24 11:45:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#issuecomment-578098382  

It might make sense to use asio file descriptor primitves to model file access, since we're in an asio context.

---

## Comment 2

> Username: mika-fischer  
> Created_at: 2020-01-24 11:49:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#issuecomment-578099544  

Note that the same problem exists in `file_stdio` and `file_win32`, so this fix is only partial.

---

## Review 3 [Commented]

> Username: mika-fischer  
> Created_at: 2020-01-24 12:35:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347924916  

📁 test/beast/http/file_body.cpp

```diff
 196 |-         fileBodyUnexpectedEofOnGet();
 199 |+ #if BOOST_BEAST_USE_POSIX_FILE
 200 |+         fileBodyUnexpectedEofOnGet<file_stdio>();
```

> Username: mika-fischer  
> Created_at: 2020-01-24 12:35:09 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370611795  

I think the stdio test can always run, no?


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-01-24 13:03:22 UTC  
> Updated_at: 2020-01-27 14:59:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#issuecomment-578121595  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=h1) Report  
> Merging [#1821](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/9068787df1de593705ffcdebc6f4bbc10ffa77ce?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1821/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1821      +/-   ##  
===========================================  
+ Coverage    95.17%   95.19%   +0.01%       
===========================================  
  Files          156      156                
  Lines        11950    11956       +6       
===========================================  
+ Hits         11373    11381       +8       
+ Misses         577      575       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/1821/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.56% <100%> (+0.06%)` | :arrow_up: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1821/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `83.33% <100%> (+1.36%)` | :arrow_up: |  
| [include/boost/beast/core/impl/file\_posix.ipp](https://codecov.io/gh/boostorg/beast/pull/1821/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9wb3NpeC5pcHA=) | `83.21% <0%> (+0.69%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1821/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `97.32% <0%> (+0.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=footer). Last update [9068787...8f61e52](https://codecov.io/gh/boostorg/beast/pull/1821?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 13:17:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347945581  

📁 include/boost/beast/core/impl/file_posix.ipp

```diff
 285 |         {
 286 |             // short read
 287 |+             ec = net::error::eof;
```

> Username: vinniefalco  
> Created_at: 2020-01-24 13:17:15 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370627900  

I don't think we should return a `net::error` from any "...file" classes. These are supposed to be independent of networking, in fact at some point it should be factored out into its own library because it is generally useful. If you think about it, returning this error could confuse an HTTP read with a file body into thinking the peer shut down the socket, when it didn't.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 13:17:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347945926  

📁 include/boost/beast/core/impl/file_stdio.ipp

```diff
 301 |+     if (nread == 0 && std::feof(f_))
 302 |+     {
 303 |+         ec = net::error::eof;
```

> Username: vinniefalco  
> Created_at: 2020-01-24 13:17:56 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370628162  

same thing, a `net::error` is not the right error


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 13:19:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347946840  

📁 test/beast/http/file_body.cpp

```diff
 141 |+     static void move_to_eof(file_posix& file)
 142 |+     {
 143 |+         ::lseek(file.native_handle(), 0, SEEK_END);
```

> Username: vinniefalco  
> Created_at: 2020-01-24 13:19:48 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370628878  

These could be done portably using `File::pos` and `File::seek`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 13:20:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347947289  

📁 test/beast/http/file_body.cpp

```diff
 157 |+ #endif
 158 |+ 
 159 |+     template<class FileType>
```

> Username: vinniefalco  
> Created_at: 2020-01-24 13:20:40 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370629229  

`class File` would be better (Since `File` is a named requirement)


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 13:22:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-347948124  

📁 test/beast/http/file_body.cpp

```diff
 218 |+     #if BOOST_BEAST_USE_WIN32_FILE
 219 |+         fileBodyUnexpectedEofOnGet<file_win32>();
 220 |+     #endif
```

> Username: vinniefalco  
> Created_at: 2020-01-24 13:22:20 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370629911  

Please move these calls to below the calls to `doTestFileBody` so that the order of invocation matches the order of definitions in the source file.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 19:24:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-348172284  

📁 include/boost/beast/http/impl/file_body_win32.hpp

```diff
 161 |             if(ec)
 162 |                 return boost::none;
 163 |+             if (nread == 0)
```

> Username: vinniefalco  
> Created_at: 2020-01-24 19:24:07 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370802103  

what if `nread > 0` but `nread` is also less than what we expect in terms of reading everything in the file?

> Username: mika-fischer  
> Created_at: 2020-01-24 20:17:15 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370824026  

Then we'll get the short read error in the next call to get.  
  
This behavior is similar to POSIX read and fread.

> Username: madmongo1  
> Created_at: 2020-01-24 20:28:25 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370828259  

agree. no special handling required here.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-24 19:24:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1821#pullrequestreview-348172497  

📁 test/beast/http/error.cpp

```diff
  68 | 
  69 |         check("beast.http", error::stale_parser);
  70 |+         check("beast.http", error::short_read);
```

> Username: vinniefalco  
> Created_at: 2020-01-24 19:24:30 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370802251  

don't we already have a short read error?

> Username: vinniefalco  
> Created_at: 2020-01-24 19:25:51 UTC  
> Updated_at: 2020-01-27 13:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#discussion_r370802867  

seems not


---

## Comment 12

> Username: madmongo1  
> Created_at: 2020-01-27 13:25:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#issuecomment-578744740  

fixes #1818   
fixes #1815

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2020-01-27 13:37:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1821#issuecomment-578750029  

"file body reports short_read on eof"

---
