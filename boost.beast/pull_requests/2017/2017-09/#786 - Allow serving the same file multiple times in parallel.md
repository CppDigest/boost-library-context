# #786 Allow serving the same file multiple times in parallel [Closed]

> Username: ngladitz  
> Created at: 2017-09-20 21:42:15 UTC  
> Updated at: 2017-09-29 13:22:46 UTC  
> Closed at: 2017-09-29 13:22:46 UTC  
> Url: https://github.com/boostorg/beast/pull/786  

Without this change the second opening of an  
already opened file fails with:  
  
  "The process cannot access the file because it  
  is being used by another process"

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-09-20 22:15:41 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-330996401  

I like it, but this needs to use a constant from Boost.WinAPI and not FILE_SHARE_READ from `<windows.h>`. So you just need to change the constant to `boost::detail::winapi::FILE_SHARE_READ_`  
  
Thanks!

---

## Comment 2

> Username: ngladitz  
> Created_at: 2017-09-21 05:54:08 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331058660  

Thanks! I updated the PR accordingly.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-09-21 06:26:30 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331063711  

This looks very good! So you're serving files? Are you using `file_body`? Is it working? Did you know it uses `::TransmitFile` on Windows?

---

## Comment 4

> Username: ngladitz  
> Created_at: 2017-09-21 07:04:38 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331070677  

Yes, I am using `file_body`. Except for the issue in this MR it seems to be working well for me.  
I wasn't aware that it uses `::TransmitFile` but that sounds awesome. Thanks!  
  
One thing I am unsure about (but haven't tested either) is that the implementation is using `CreateFileA` (which is "ANSI" i.e. code page specific) but the reference seems to suggest the path is encoded in UTF-8.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-09-21 23:09:43 UTC  
> Updated_at: 2017-09-21 23:10:24 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331306242  

>is that the implementation is using CreateFileA (which is "ANSI" i.e. code page specific) but the reference seems to suggest the path is encoded in UTF-8.  
  
Yeah...eventually I will have to fix this. See: https://github.com/boostorg/beast/issues/793, pull requests welcome :)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-09-21 23:11:49 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331306568  

What happens with this change if you open the file for writing? That should be exclusive mode (i.e. produce an error)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-09-21 23:12:38 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331306688  

>I wasn't aware that it uses ::TransmitFile  
  
If you're not aware, then it is probably working :)

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-09-22 00:56:08 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331321089  

Your commit is part of version 118: https://github.com/boostorg/beast/pull/794

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2017-09-22 03:26:16 UTC  
> Updated_at: 2017-09-23 02:12:54 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331340523  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=h1) Report  
> Merging [#786](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0f5ea371c18a6797f4623a61f159326428c01400?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/786/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #786   +/-   ##  
=======================================  
  Coverage     95.7%   95.7%             
=======================================  
  Files          105     105             
  Lines        10534   10534             
=======================================  
  Hits         10082   10082             
  Misses         452     452  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=footer). Last update [0f5ea37...b3f8967](https://codecov.io/gh/boostorg/beast/pull/786?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 10

> Username: ngladitz  
> Created_at: 2017-09-22 06:26:09 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331361510  

> What happens with this change if you open the file for writing? That should be exclusive mode (i.e. produce an error)  
  
With the change opening a file twice for writing should fail but I think it would allow one open for reading and one open for writing to the same file.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-09-22 08:08:56 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331380812  

Opening a file twice for writing should always fail. However, I think opening a file for writing and also opening it for reading within the same process should also fail...

---

## Comment 12

> Username: ngladitz  
> Created_at: 2017-09-22 11:47:04 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331425398  

I've create a new set of commits.  
The first is cosmetic (I can undo it if you dislike it).  
The second only makes use of `FILE_SHARE_READ_` when the file is opened for reading.  
  
This should restore the error behavior in case the file is opened once for reading and once for writing.  
  
I think the Posix implementation currently still allows shared access under all circumstances.

---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2017-09-22 14:06:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/786#pullrequestreview-64588433  

📁 include/boost/beast/core/impl/file_win32.ipp

```diff
 124 |+     boost::detail::winapi::DWORD_ share_mode = 0;
 125 |+     boost::detail::winapi::DWORD_ creation_disposition = 0;
 126 |+     boost::detail::winapi::DWORD_ flags_and_attributes = 0;
```

> Username: vinniefalco  
> Created_at: 2017-09-22 14:06:13 UTC  
> Updated_at: 2017-09-22 14:06:32 UTC  
> Url: https://github.com/boostorg/beast/pull/786#discussion_r140502543  

This looks good!


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2017-09-22 14:07:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/786#pullrequestreview-64588924  

📁 include/boost/beast/core/impl/file_win32.ipp

```diff
 191 |+         creation_disposition = boost::detail::winapi::CREATE_ALWAYS_;
 192 |+ 
 193 |+         flags_and_attributes = 0x08000000; // FILE_FLAG_SEQUENTIAL_SCAN
```

> Username: vinniefalco  
> Created_at: 2017-09-22 14:07:58 UTC  
> Url: https://github.com/boostorg/beast/pull/786#discussion_r140502982  

Perhaps we should add `boost::detail::winapi::FILE_FLAG_SEQUENTIAL_SCAN_`? It wouldn't become available until Boost 1.66.0 but can get it in now, see: https://github.com/boostorg/winapi/tree/develop/include/boost/detail/winapi

> Username: ngladitz  
> Created_at: 2017-09-22 15:34:32 UTC  
> Url: https://github.com/boostorg/beast/pull/786#discussion_r140525729  

Both `FILE_FLAG_RANDOM_ACCESS_` and `FILE_FLAG_SEQUENTIAL_SCAN_`?  
  
Not entirely sure what you mean by getting them now.  
Copy the headers into Beast? Copy just those definitions? Require consumers to get the pre-release headers?


---

## Comment 15

> Username: vinniefalco  
> Created_at: 2017-09-22 14:09:27 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331457586  

It looks like you're on a roll, and getting good use from `file_body`. What do you think about this issue: https://github.com/boostorg/beast/issues/723

---

## Comment 16

> Username: ngladitz  
> Created_at: 2017-09-22 15:52:38 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331486253  

> It looks like you're on a roll, and getting good use from file_body. What do you think about this issue: #723  
  
I could think of a use case or two for this but it might be a while before I get around to it.  
  
e.g. I've got large archives (files) containing deflated streams. I was pondering serving those streams individually and having the browser transparently inflate them (where supported). Might require decorating the data (not sure if you can e.g. just stick a gzip header in front of a deflate stream).

---

## Comment 17

> Username: vinniefalco  
> Created_at: 2017-09-22 20:09:52 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331548687  

These examples also have an extraneous strand for `listener`:  
* http-server-async-ssl  
* websocket-server-async  
* websocket-server-async-ssl

---

## Comment 18

> Username: ngladitz  
> Created_at: 2017-09-23 07:03:35 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331615933  

> These examples also have an extraneous strand for listener:  
>   
>     http-server-async-ssl  
>     websocket-server-async  
>     websocket-server-async-ssl  
  
Different PR/Issue presumably?

---

## Comment 19

> Username: vinniefalco  
> Created_at: 2017-09-23 12:31:42 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331630936  

>Different PR/Issue presumably?  
  
I would just put them all into the one commit that already removes the strand

---

## Comment 20

> Username: ngladitz  
> Created_at: 2017-09-23 12:35:29 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331631126  

> I would just put them all into the one commit that already removes the strand  
  
Sorry I mean what do strands have to do with the file_win32 changes (i.e. this PR)?

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2017-09-23 12:48:49 UTC  
> Url: https://github.com/boostorg/beast/pull/786#issuecomment-331631781  

oops...looks like I commented in the wrong commit!!!

---
