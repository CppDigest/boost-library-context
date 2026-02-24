# #1227 add socks proxy for example. [Closed]

> Username: Jackarain  
> Created at: 2018-08-15 09:34:47 UTC  
> Updated at: 2022-06-22 20:05:48 UTC  
> Closed at: 2022-06-22 20:05:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1227  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-15 09:50:18 UTC  
> Updated_at: 2019-09-17 16:20:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-413148918  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=h1) Report  
> Merging [#1227](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/55fd67b8740d79ec63e6c17107d447db7c9a3082?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1227/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1227      +/-   ##  
===========================================  
+ Coverage    94.45%   94.46%   +<.01%       
===========================================  
  Files          156      156                
  Lines        11907    11907                
===========================================  
+ Hits         11247    11248       +1       
+ Misses         660      659       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1227/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `83.41% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=footer). Last update [55fd67b...753cea0](https://codecov.io/gh/boostorg/beast/pull/1227?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-08-15 13:43:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-413201527  

Wow, there's a lot here :)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-08-19 01:18:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-414096304  

How do we test this?

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:18:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444678  

📁 example/socks/Jamfile

```diff
   9 |+ 
  10 |+ exe socks :
  11 |+     http.cpp
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:18:51 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089278  

Is http.cpp the correct name?

> Username: madmongo1  
> Created_at: 2019-12-15 12:44:58 UTC  
> Updated_at: 2019-12-15 12:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357976248  

confirmed changed


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:18:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444680  

📁 example/socks/Jamfile

```diff
   1 |+ #
   2 |+ # Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:18:59 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089280  

You own this now, I think your name should be on it :)

> Username: madmongo1  
> Created_at: 2019-12-15 12:45:26 UTC  
> Updated_at: 2019-12-15 12:45:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357976269  

confirmed updated


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:21:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444700  

📁 example/socks/error.hpp

```diff
  80 |+         }
  81 |+ 
  82 |+         virtual std::string message(int e) const
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:21:10 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089313  

`override`

> Username: madmongo1  
> Created_at: 2019-12-15 12:45:38 UTC  
> Updated_at: 2019-12-15 12:45:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357976297  

confirmed fixed


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:22:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444708  

📁 example/socks/http.cpp

```diff
  12 |+ 
  13 |+ #include <boost/beast/core.hpp>
  14 |+ #include <boost/beast/http.hpp>
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:22:00 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089324  

It is better only to include the things you actually need instead of including everything.  
  
Also, is http.cpp the intended file name?

> Username: madmongo1  
> Created_at: 2019-12-15 12:49:03 UTC  
> Updated_at: 2019-12-15 12:49:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357976483  

confirmed updated


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:22:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444720  

📁 example/socks/http.cpp

```diff
  44 |+ }
  45 |+ 
  46 |+ inline bool parse_socks_url(const std::string& url, socks_url& result)
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:22:50 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089336  

If we turn this into a library component (e.g. in boost/beast/experimental/) then it will need tests

> Username: vinniefalco  
> Created_at: 2019-12-15 13:05:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357977541  

Update: There's a separate URL library with docs and tests, so this url code can be okay as-is until that library becomes part of boost.

> Username: madmongo1  
> Created_at: 2019-12-15 14:05:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357981263  

noted


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:24:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444741  

📁 example/socks/http.cpp

```diff
  56 |+         if (!is_scheme_char(c))
  57 |+             return false;
  58 |+         result.scheme.push_back(static_cast<char>(std::tolower(c)));
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:24:58 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089359  

I don't think you want to use `tolower` here because that respects the current code page or whatever it is called. URLs can only use ascii I believe? Maybe use this one instead:  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/include/boost/beast/core/string.hpp#L33  
  
I can make that a public interface

> Username: madmongo1  
> Created_at: 2019-12-15 14:17:56 UTC  
> Updated_at: 2019-12-15 14:17:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r357981970  

has been refactored in terms of `iequals`


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:26:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444752  

📁 example/socks/http.cpp

```diff
 255 |+ 
 256 |+     socks_url url;
 257 |+     if (!parse_socks_url(socks_server, url))
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:26:02 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089372  

`parse_socks_url` does not return an error code, so `ec` will never get set


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:28:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444776  

📁 example/socks/http.cpp

```diff
 274 |+         return fail(ec, "connect");
 275 |+ 
 276 |+     socks::async_handshake(socket, host, static_cast<unsigned short>(std::atoi(port.c_str())),
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:28:02 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089393  

Where does one get a socks server to test with?

> Username: vinniefalco  
> Created_at: 2018-08-19 01:29:28 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089412  

If I am reading this right, then to operate a socks client you simply connect to the server and perform the handshake. From that point on, the socket behaves as a transparent tunnel? So no additional state has to be maintained for the lifetime of the connection, and the proxy connection algorithm can be expressed simply as a free function?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:30:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444814  

📁 example/socks/http.cpp

```diff
 325 |+             "Usage: socsk <host> <port> <target> <socks[4|5]://[user[:password]@]server:port>\n" <<
 326 |+             "Example:\n" <<
 327 |+             "    socsk www.example.com 80 / socks5://socks5server.com:1080\n";
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:30:36 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089424  

The example line is wrong. "socsk" is misspelled. I think this program should be renamed to "socks-client-coro" and the source file renamed to socks_client_coro.cpp


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:33:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444846  

📁 example/socks/socks_client.hpp

```diff
  22 |+ #include <memory>
  23 |+ 
  24 |+ using boost::asio::ip::tcp;
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:33:50 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089442  

We shouldn't have a `using` directive at file scope in a header file. I know it sucks typing all of that out but it is better for users if we don't do this.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:34:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444853  

📁 example/socks/socks_client.hpp

```diff
  31 |+     {
  32 |+     public:
  33 |+         enum {
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:34:07 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089447  

these enums don't need to be public


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:36:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444879  

📁 example/socks/socks_client.hpp

```diff
  69 |+ 
  70 |+     public:
  71 |+         explicit socks_client(tcp::socket& socket,
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:36:30 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089476  

the client should be templated on `NextLayer` and only require the SyncReadStream and SyncWriteStream concepts for synchronous client operation, or the AsyncReadStream and AsyncWriteStream concepts for asynchronous client operation.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:37:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444894  

📁 example/socks/socks_client.hpp

```diff
  89 |+             {
  90 |+             case SOCKS_VERSION_5:
  91 |+                 boost::asio::spawn(socket_.get_io_context(), [this, self]
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:37:55 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089488  

The implementation of the composed operation should not use a stackful coroutine. Instead, it should be written using the stackless coroutine API that comes with Asio. There's a tutorial in the Beast docs which can guide you:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_io/writing_composed_operations.html  
  
I can also help you get started by answering questions and give you some initial declarations, if you desire.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:39:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444907  

📁 example/socks/socks_client.hpp

```diff
 109 |+     private:
 110 |+         template<typename type, typename source>
 111 |+         type read(source& p)
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:39:16 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089497  

This can be a `static` function. And even better, move it into a base class `detail::socks_base` so that it is not templated on `Handler`. Rationale: By making `read` and `write` needlessly dependent on a template parameter, the resulting executable will have extra copies of these functions for programs which use more than one handler type.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:41:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444931  

📁 example/socks/socks_client.hpp

```diff
 126 |+         void do_socks5_handshake(boost::asio::yield_context yield)
 127 |+         {
 128 |+             boost::asio::streambuf request;
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:41:36 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089525  

instead of `asio::streambuf`, consider using `beast::static_buffer` or `beast::flat_static_buffer`. Rationale: If the strings which make up the SOCKS handshake have an upper limit on their size, use of a static buffer can eliminate the need for a dynamic memory allocation. If you do this, you may need to also use `handler_ptr` since a composed operation state containing a static buffer is non-trivial.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-19 01:42:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-147444951  

📁 example/socks/socks_client.hpp

```diff
 148 |+                 request.commit(bytes_to_write);
 149 |+                 boost::asio::async_write(socket_, request,
 150 |+                     boost::asio::transfer_exactly(bytes_to_write), yield[ec]);
```

> Username: vinniefalco  
> Created_at: 2018-08-19 01:42:53 UTC  
> Updated_at: 2019-09-17 16:06:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r211089540  

You can probably simplify this line (and others like it) to:  
```  
boost::asio::async_write(socket_, request.data(), yield[ec]);  
```


---

## Comment 20

> Username: Jackarain  
> Created_at: 2018-08-19 20:12:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-414152710  

all right now, almost rewritten, thank you for taking the time to review the code.

---

## Comment 21

> Username: Jackarain  
> Created_at: 2018-08-19 20:16:19 UTC  
> Updated_at: 2019-09-05 14:06:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-414152927  

test server:   
http://www.gatherproxy.com/zh/sockslist

---

## Comment 22

> Username: stale[bot]  
> Created_at: 2018-10-19 04:56:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-431244865  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 23

> Username: dmorilha  
> Created_at: 2019-07-19 19:30:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-513349262  

very cool!

---

## Comment 24

> Username: vinniefalco  
> Created_at: 2019-09-10 02:20:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-529739814  

I'm working on integrating this, I will have a branch for you to look at this week.

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2019-09-10 21:27:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-530127534  

> all right now, almost rewritten, thank you for taking the time to review the code.  
  
I was just comparing the original code versus your new code and I am very impressed by the improvements. This is close to being ready to merge (sorry for taking so long). However there are still some things which need to be done. If you could reset your pull request to this branch, I will comment on the remaining changes: https://github.com/vinniefalco/beast/commits/socks  
  
If you want to squash the commits marked "[FOLD]" that would be fine as well, so it is all in one big commit. I left them unsquashed so you can see each change that I made, and hopefully understand the rationale (I'm happy to answer any questions if you have them).

---

## Comment 26

> Username: Jackarain  
> Created_at: 2019-09-11 02:06:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-530188485  

Great!  
I will reset my pull request to a new branch! Thanks again!

---

## Comment 27

> Username: vinniefalco  
> Created_at: 2019-09-17 11:17:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-532176751  

I don't see the reset?

---

## Comment 28

> Username: vinniefalco  
> Created_at: 2019-09-17 16:15:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-532292444  

Reviewing...

---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-17 23:55:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289607862  

📁 example/contrib/socks-client/socks/detail/char_type.hpp

```diff
 126 |+         "e0", "e1", "e2", "e3", "e4", "e5", "e6", "e7", "e8", "e9", "ea", "eb", "ec", "ed", "ee", "ef",
 127 |+         "f0", "f1", "f2", "f3", "f4", "f5", "f6", "f7", "f8", "f9", "fa", "fb", "fc", "fd", "fe", "ff"
 128 |+     };
```

> Username: vinniefalco  
> Created_at: 2019-09-17 23:55:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325432776  

Here we have 256 null-terminated, two character strings which is 768 bytes. If we remove the null terminators we can save a third of that and bring this down to 512 bytes, like this:  
```  
static char const* const s = "00" "01" "02" ...  
```  
(Note how the commas are removed)  
  
Then we can just do `return string_view{s+c*2, 2};`


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-17 23:57:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289608403  

📁 example/contrib/socks-client/socks/detail/host.hpp

```diff
  22 |+ 	host_ipv6,
  23 |+ 	host_domain,
  24 |+ 	host_invalid,
```

> Username: vinniefalco  
> Created_at: 2019-09-17 23:57:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433213  

There's tabs here instead of spaces


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289608716  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  29 |+     const std::string& hostname,
  30 |+     unsigned short port,
  31 |+     std::string const& username,
```

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:04 UTC  
> Updated_at: 2019-09-17 23:59:05 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433458  

I think these `std::string const&` parameters would be better as `string_view`


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289608794  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  43 |+     unsigned short port,
  44 |+     std::string const& username,
  45 |+     std::string const& password,
```

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433524  

`string_view` instead of `std::string const&`


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289608882  

📁 example/contrib/socks-client/socks/handshake.hpp

```diff
  46 |+     bool use_hostname,
  47 |+     Handler&& handler);
  48 |+ 
```

> Username: vinniefalco  
> Created_at: 2019-09-17 23:59:43 UTC  
> Updated_at: 2019-09-17 23:59:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433597  

Where are the synchronous handshake functions?


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:00:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289609070  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  31 |+ namespace detail {
  32 |+ 
  33 |+ template<typename type, typename source>
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:00:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433746  

`type` and `source` should be capitalized per the style


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:01:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289609202  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  34 |+ type read(source& p)
  35 |+ {
  36 |+     type ret = 0;
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:01:07 UTC  
> Updated_at: 2019-09-18 00:01:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325433847  

We should use `BOOST_STATIC_ASSERT` to ensure that `Type` is integral (and maybe even unsigned)


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:01:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289609395  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  41 |+ 
  42 |+ template<typename type, typename target>
  43 |+ void write(type v, target& p)
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:01:57 UTC  
> Updated_at: 2019-09-18 00:01:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325434011  

For `read` and `write` why don't we just use `char const*&`  and `char*&` instead of `Source` and `Target`?


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:02:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289609491  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  53 |+     class Handler,
  54 |+     class Buffer,
  55 |+     class base_type = boost::beast::async_base<
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:02:18 UTC  
> Updated_at: 2019-09-18 00:02:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325434085  

The `base_type` template parameter is not needed, it is better to just repeat the long typename where needed.


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:03:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289609674  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  52 |+     class Stream,
  53 |+     class Handler,
  54 |+     class Buffer,
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:03:00 UTC  
> Updated_at: 2019-09-18 00:03:01 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325434226  

We should probably not make `Buffer` a template parameter but instead have the implementation just choose the buffer type that is most appropriate


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:06:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289610567  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  55 |+     class base_type = boost::beast::async_base<
  56 |+         Handler, typename Stream::executor_type>>
  57 |+ class socks4_op : public base_type
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:06:54 UTC  
> Updated_at: 2019-09-18 00:06:55 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325434959  

`socks4_op` belongs in the `detail` namespace since it is not a public interface


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:08:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289610921  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
  86 |+         auto& request = *request_;
  87 |+ 
  88 |+         switch (ec ? 3 : step_)
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:08:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325435222  

Instead of `switch` and `step_`, this should use `<boost/asio/coroutine.hpp>` and the macros `BOOST_ASIO_CORO_REENTER`, `BOOST_ASIO_CORO_YIELD`, it will make the code easier to reason about.


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:17:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289612889  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 113 |+             write<uint8_t>(0, req); // NULL.
 114 |+ 
 115 |+             request.commit(bytes_to_write);
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:17:04 UTC  
> Updated_at: 2019-09-18 00:18:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325436858  

Instead of building the request here, in `case 0`, we should do it in the constructor. This way, we do not have to store the hostname and username strings, avoiding a memory allocation. The code to build the request can be placed into a separate function, called from both the synchronous and asynchronous handshake algorithms.


---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:23:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289614095  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 161 |+     using BufferPtr = std::unique_ptr<Buffer>;
 162 |+     BufferPtr request_{ new Buffer() }; // std::make_unique c++14 or later.
 163 |+     BufferPtr response_{ new Buffer() };
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:23:13 UTC  
> Updated_at: 2019-09-18 00:23:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325437886  

I don't think it is necessary to have two buffers. After sending the request, we can free the memory and then reallocate. Or perhaps we can recycle the one buffer - the smallest request is 9 bytes, and it looks like the response is always 8 bytes, so we can get away with just storing one `std::unique_ptr<char[], my_deleter>`


---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:23:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289614147  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 173 |+     class Handler,
 174 |+     class Buffer,
 175 |+     class base_type = boost::beast::async_base<
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:23:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325437924  

We can probably do away with `Buffer` and `base_type`


---

## Review 44 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:27:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289615021  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 197 |+         , use_hostname_(use_hostname)
 198 |+     {
 199 |+         (*this)({}, 0); // start the operation
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:27:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325438671  

If we can build the initial request in the constructor that would be best, although it looks like we might need to save the username and password. Although we could do that in a single buffer instead of using two strings.


---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:27:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289615078  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 213 |+         auto& request = *request_;
 214 |+ 
 215 |+         switch (ec ? 10 : step_)
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:27:49 UTC  
> Updated_at: 2019-09-18 00:27:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325438717  

Instead of switch and step_, this should use <boost/asio/coroutine.hpp> and the macros BOOST_ASIO_CORO_REENTER, BOOST_ASIO_CORO_YIELD, it will make the code easier to reason about.


---

## Review 46 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:30:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289615626  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 351 |+             {
 352 |+                 write<uint8_t>(detail::SOCKS5_ATYP_DOMAINNAME, req); // atyp, domain name.
 353 |+                 BOOST_ASSERT(hostname_.size() <= 255);
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:30:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439174  

If protocol strings have upper limits, these need to be enforced at runtime by something stronger than just an assert. The handshaking functions need to check the parameters and throw if they are out of bounds, e.g.  
```  
    if (hostname.size() > 255)  
        BOOST_THROW_EXCEPTION(std::out_of_range{"hostname.size() > 255"});  
```


---

## Review 47 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:31:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289615942  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 546 |+     const std::string& hostname,
 547 |+     unsigned short port,
 548 |+     std::string const& username,
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:31:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439425  

Should use `string_view` instead of `std::string`, and throw if the protocol length limits are exceeded (e.g. `hostname.size() > 255`)


---

## Review 48 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616002  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 558 |+         net::associated_allocator_t<HandlerType>>:: template rebind_alloc<char> >;
 559 |+ 
 560 |+     socks4_op<AsyncStream, HandlerType, Buffer>(stream, init.completion_handler,
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439460  

This needs to use the new `async_initiate`, perhaps @djarek can help?


---

## Review 49 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616055  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 575 |+     unsigned short port,
 576 |+     std::string const& username,
 577 |+     std::string const& password,
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:28 UTC  
> Updated_at: 2019-09-18 00:32:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439507  

`string_view` instead of `std::string`, and throw on length overflows


---

## Review 50 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616094  

📁 example/contrib/socks-client/socks/impl/handshake.hpp

```diff
 588 |+         net::associated_allocator_t<HandlerType>>:: template rebind_alloc<char> >;
 589 |+ 
 590 |+     socks5_op<AsyncStream, HandlerType, Buffer>(stream, init.completion_handler,
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:32:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439537  

This needs to use the new `async_initiate`


---

## Review 51 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:34:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616481  

📁 example/contrib/socks-client/socks/query.hpp

```diff
  33 |+         using value_type = std::pair<string_view, string_view>;
  34 |+         using difference_type = std::ptrdiff_t;
  35 |+         using reference = value_type&;
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:34:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439841  

Actually this is not right, `reference` can't be an actual reference it has to be a value type, e.g.  
```  
using reference = value_type;  
```  
  
And `value_type` can't be a `std::pair` or else the `const_iterator::operator->` won't work (it can't return a pointer). Instead, I suggest you use this declaration for `value_type`:  
```  
struct value_type  
{  
  string_view name;  
  string_view value;  
  value_type const* operator->() const noexcept  
  {  
    return this;  
  }  
};  
```  
  
Then you can use `using pointer = value_type const;` and things will work right


---

## Review 52 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616617  

📁 example/contrib/socks-client/socks/query.hpp

```diff
  99 |+ 
 100 |+         string_view
 101 |+         value() const noexcept
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439951  

You won't need `key` and `value` if you use the struct `value_type` above


---

## Review 53 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616656  

📁 example/contrib/socks-client/socks/query.hpp

```diff
 117 |+     };
 118 |+ 
 119 |+     BOOST_BEAST_DECL
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:27 UTC  
> Updated_at: 2019-09-18 00:35:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439982  

`BOOST_BEAST_DECL` is not needed here (my fault)


---

## Review 54 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616670  

📁 example/contrib/socks-client/socks/query.hpp

```diff
 124 |+     }
 125 |+ 
 126 |+     BOOST_BEAST_DECL
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:35:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325439996  

`BOOST_BEAST_DECL` is not needed here (my fault)


---

## Review 55 [Commented]

> Username: vinniefalco  
> Created_at: 2019-09-18 00:36:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1227#pullrequestreview-289616872  

📁 example/contrib/socks-client/socks/uri.hpp

```diff
  34 |+     ~uri() = default;
  35 |+ 
  36 |+     BOOST_BEAST_DECL
```

> Username: vinniefalco  
> Created_at: 2019-09-18 00:36:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#discussion_r325440170  

all instances of `BOOST_BEAST_DECL` should be replaced with `inline` (my fault)


---

## Comment 56

> Username: vinniefalco  
> Created_at: 2019-09-29 15:13:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-536311615  

Do you need help?

---

## Comment 57

> Username: Jackarain  
> Created_at: 2019-09-30 10:22:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-536500201  

> Do you need help?  
  
Yes, I need more help, and more improvements, thank you! :blush:

---

## Comment 58

> Username: vinniefalco  
> Created_at: 2019-09-30 12:56:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-536548470  

Which part do you need help with?

---

## Comment 59

> Username: illera88  
> Created_at: 2019-10-29 21:44:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-547643464  

Hi @Jackarain ,  
  
looking forward for this to be merged.

---

## Comment 60

> Username: vinniefalco  
> Created_at: 2019-11-23 01:02:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-557748270  

I haven't forgotten about this, it will go in after 1.72 is released.

---

## Comment 61

> Username: vinniefalco  
> Created_at: 2019-11-24 18:16:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-557913629  

I believe this also has to be updated for asio's latest changes: completion token default value and C++14 auto return type deduction.

---

## Comment 62

> Username: omegacoleman  
> Created_at: 2020-04-21 15:09:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-617240804  

in terms of anyone willing to push this further, I started to do sth I could in my repo https://github.com/omegacoleman/beast here, feel free to pick them.

---

## Comment 63

> Username: vinniefalco  
> Created_at: 2020-04-21 16:57:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-617285390  

The problem is that SOCKS is an extensive protocol, and I do not feel comfortable putting something that only partially supports it in as an example.

---

## Comment 64

> Username: omegacoleman  
> Created_at: 2020-04-21 18:17:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1227#issuecomment-617330399  

I opted in because i thought it's useful to show others how to build protocols on top of beast. I was doing an unreleased project porting amazon s3 api to beast and the querystring parser here helped.  
  
The incompleteness might not matter so much as an example, as boost.spirit got mini-xml examples which is also incomplete but very useful.

---
