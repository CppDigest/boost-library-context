# #593 Prototype file_body_linux implementation [Closed]

> Username: chriskohlhoff  
> Created at: 2017-07-04 12:56:05 UTC  
> Updated at: 2017-07-20 14:50:21 UTC  
> Closed at: 2017-07-20 14:50:21 UTC  
> Url: https://github.com/boostorg/beast/pull/593  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-04 13:52:52 UTC  
> Updated_at: 2017-07-06 15:36:10 UTC  
> Url: https://github.com/boostorg/beast/pull/593#issuecomment-312883778  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=h1) Report  
> Merging [#593](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=desc) into [review-addendum-1](https://codecov.io/gh/vinniefalco/Beast/commit/0111380d6720591408fc852f22739a5d52402733?src=pr&el=desc) will **decrease** coverage by `0.05%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/593/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree)  
  
```diff  
@@                  Coverage Diff                  @@  
##           review-addendum-1     #593      +/-   ##  
=====================================================  
- Coverage              93.81%   93.76%   -0.06%       
=====================================================  
  Files                     97       97                
  Lines                   7507     7507                
=====================================================  
- Hits                    7043     7039       -4       
- Misses                   464      468       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/file\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpbGVfYm9keS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/file\_body\_stdc.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpbGVfYm9keV9zdGRjLmhwcA==) | `83.72% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `57.14% <0%> (-14.29%)` | :arrow_down: |  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `83.66% <0%> (-1.97%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=footer). Last update [0111380...40efb4a](https://codecov.io/gh/vinniefalco/Beast/pull/593?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 14:29:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/593#pullrequestreview-47879132  

📁 include/beast/http/file_body_linux.hpp

```diff
  13 |+ #else
  14 |+ # define BEAST_NO_LINUX_FILE 1
  15 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2017-07-04 14:29:17 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125485669  

Should we use `<boost/config.hpp>` and `BOOST_LINUX` or whatever it is? Also, can we please check if the macro is already set:  
```  
#ifndef BEAST_NO_LINUX_FILE  
# ifdef __linux__  
#  define BEAST_NO_LINUX_FILE 0  
# else  
#  define BEAST_NO_LINUX_FILE 1  
# endif  
#endif  
```

> Username: chriskohlhoff  
> Created_at: 2017-07-04 22:27:00 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125533871  

I've added the extra `#ifdef` check. I think a proper feature test should be left until this gets merged properly -- it might really be better as a "posix" rather than "linux" file body, as the implementation will just differ in the write specialisations.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 14:32:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/593#pullrequestreview-47879985  

📁 include/beast/http/file_body_linux.hpp

```diff
  83 |+             value_type tmp(std::move(other));
  84 |+             std::swap(file_, tmp.file_);
  85 |+             std::swap(size_, tmp.size_);
```

> Username: vinniefalco  
> Created_at: 2017-07-04 14:32:55 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125486404  

I think that the move assignment should specify that the moved-from object is left as-if default-constructed. You like keeping objects around (e.g. http_worker) and I imagine that the moved-from object could be left with an open file unintentionally. There's precedent for this, I believe that's how `basic_io_object` works (correct me if wrong). After a move you get a clean socket ready for reuse.

> Username: chriskohlhoff  
> Created_at: 2017-07-04 22:14:35 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125533341  

The move-construction into `tmp` should already cover this.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 14:34:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/593#pullrequestreview-47880278  

📁 include/beast/http/file_body_linux.hpp

```diff
 120 |+             case file_mode::scan:   f = O_RDONLY; break;
 121 |+             case file_mode::write:  f = O_WRONLY | O_CREAT; break;
 122 |+             case file_mode::append: f = O_WRONLY | O_APPEND; break;
```

> Username: vinniefalco  
> Created_at: 2017-07-04 14:34:28 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125486675  

Consider these additional mode flags:  
https://github.com/vinniefalco/NuDB/blob/master/include/nudb/impl/posix_file.ipp#L206

> Username: chriskohlhoff  
> Created_at: 2017-07-04 22:16:04 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125533388  

You mentioned elsewhere that you had another implementation of posix-based files you can use, so I assume you'll merge that before this becomes a real feature. I only planned to implement just enough here to support the prototype.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 14:35:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/593#pullrequestreview-47880539  

📁 include/beast/http/file_body_linux.hpp

```diff
 131 |+             if(::fstat(file_, &s) == -1)
 132 |+             {
 133 |+                 ec.assign(errno, beast::system_category());
```

> Username: vinniefalco  
> Created_at: 2017-07-04 14:35:37 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125486911  

`beast::` not necessary, its in `file_body_stdc` just because that code is extracted into the docs (Which doesn't assume the beast:: namespace). It doesn't hurt though. Although maybe when we put this in the boost:: namespace it could cause trouble.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 14:36:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/593#pullrequestreview-47880734  

📁 include/beast/http/file_body_linux.hpp

```diff
 138 |+             size_ = s.st_size;
 139 |+             ec.assign(0, ec.category());
 140 |+             signal_init<SIGPIPE>();
```

> Username: vinniefalco  
> Created_at: 2017-07-04 14:36:21 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125487074  

Why? Just wondering. Is this unfriendly to the app? Are we taking up a signal slot or something? I don't really speak signals.

> Username: chriskohlhoff  
> Created_at: 2017-07-04 22:17:28 UTC  
> Updated_at: 2017-07-04 22:26:32 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125533442  

The `sendfile()` function raises a SIGPIPE signal if the connection is broken while it is still writing data. This terminates the process by default. On linux this is suppressed for functions like `sendmsg` by passing `MSG_NOSIGNAL`, but it seems there is no equivalent for `sendfile`.

> Username: vinniefalco  
> Created_at: 2017-07-04 22:40:06 UTC  
> Url: https://github.com/boostorg/beast/pull/593#discussion_r125534367  

We don't want `terminate` heh


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-07-05 18:26:21 UTC  
> Url: https://github.com/boostorg/beast/pull/593#issuecomment-313186598  

I've incorporated this into an experimental branch, but there is considerable work to do on the concepts and basic implementation. I feel that we have sufficiently proved that the technique is viable so I will close this now and work on refining the basics, then revisit this for a full public interface. Thanks for helping!

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-07-08 07:06:36 UTC  
> Url: https://github.com/boostorg/beast/pull/593#issuecomment-313839460  

I have added the **File** concept and implemented three models:  
`file_stdio`, `file_posix`, and `file_win32`  
  
The type alias `file` refers to the best choice for the platform. Users can always ask for a specific type of file like `file_stdio` if they want.  
  
There is now a `file_body` which is an alias for `basic_file_body<file>`. It uses the **File** concept to implement the traditional reader and writer.  
  
Finally, I have added `file_win32_body.ipp` . This specializes the `basic_file_body` class for `file_win32`. There are no user-facing types anymore (like `file_body_win32`), except of course for the specialization.  The implementation for `basic_file_body<file_win32>` is rock solid. It hooks in at the lowest layer (`write_some` and `async_write_some`). The preprocessor checks are correct, for example if they don't have overlapped (if that even matters anymore).  
  
The implementation defaults to not using TransmitFile if the body is chunked. I added a feature, `serializer::limit` which lets the caller control the maximum number of bytes used in a call to write, and the TransmitFile implementation respects this. The code is also ready to enable sending just a range of a file (there is no interface to set it yet though).  
  
There is a small infrastructure that provides tools to make this type of optimization easier. I had to put the two-phase construction back (i.e. `init(error_code&)` in reader and writer). The serializer allows accessing the internal instance of `reader`, so you can store state variables there instead of putting them on the message. And Beast now supports readers that want to construct with a non-const message. In other words, for algorithms which require modifying members of the body, such as hidden state. In the case of `file_body`, reading from the file counts as logically non-const since it updates the file position, even though the reader is bitwise-const.  
  
Version 76 has the `file_body_win32`: https://github.com/vinniefalco/Beast/tree/v76  
  
Some benchmarks:  
```  
http-server-fast  
  
100,000 requests to data_64K.html  
file_stdio                      1,840 req/s     118,151Kb/s  
file_win32                      1,854 req/s     118,997Kb/s  
file_body_win32                 2,611 req/s     167,659Kb/s  
file_body_win32, tx_header      2,524 req/s     162,035Kb/s  
  
100,000 requests to data_1K.html  
file_stdio                      4,678 req/s     5,135 Kb/s  
file_win32                      4,824 req/s     5,295Kb/s  
file_body_win32                 4,542 req/s     4,985Kb/s  
file_body_win32, tx_header      4,727 req/s     5,188Kb/s  
```  
  
"tx_reader" is an experiment in using `::TransmitFile` to also send the header. It requires a memory allocation though, to flatten the buffer sequence holding the header. Its not great enough to keep, and the code is simpler without it. As you can see, the implementation holds its own for large files although for small ones there's a tiny loss. I could add a threshold on the size of the range if you think its a good idea.  
  
I feel really good about this, unfortunately it totally breaks the posix port but I guess you expected that. Please let me know your thoughts before I merge v76 (code review?)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-07-16 14:28:00 UTC  
> Url: https://github.com/boostorg/beast/pull/593#issuecomment-315612714  

@chriskohlhoff This needs to be updated, as I have changed the concepts to better support the use-case for `::sendfile` and `::Transmitfile`. I do not develop on Linux so I don't have the setup necessary to make this production quality.

---
