# #1815 - file_body_win32 causes system errors [Closed]

> Username: mika-fischer  
> Created at: 2020-01-21 13:30:22 UTC  
> Updated at: 2020-02-05 14:40:54 UTC  
> Closed at: 2020-02-05 14:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1815  

ASIO takes some care to translate system errors to asio error codes.  
  
When transfering a basic_file_body<file_win32> (i.e. using TransmitFile), and there is an error, I receive Win32 system errors. In particular, I've observed  
  
- ERROR_NETNAME_DELETED  
- WSAEINTR  
  
It's probably a good idea to map these errors like ASIO does it (see for instance https://github.com/boostorg/asio/blob/6534af41b471288091ae39f9ab801594189b6fc9/include/boost/asio/detail/impl/socket_ops.ipp#L842-L857)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-21 15:01:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-576721395  

It should be doing this alerady:  
https://github.com/boostorg/beast/blob/46ac848fa46ce5b81f2bb5b221774d15c0cdd128/include/boost/beast/http/impl/file_body_win32.hpp#L517

---

## Comment 2

> Username: mika-fischer  
> Created at: 2020-01-21 15:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-576728568  

How do you mean? This does not translate anything, it just returns a system_error with the Windows-specific error code.  
  
Check my link above to see how ASIO takes care to re-write these Windows-specific error codes to something platform-agnostic...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-21 15:36:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-576738296  

Ah yes I see what you mean.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-01-22 16:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577257919  

@mika-fischer Thanks for taking the time to let us know. I'm making a start on this issue today.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-22 20:39:14 UTC  
> Updated at: 2020-01-22 20:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577374987  

Before making any changes I felt it best to refer back to the orignal documents describing the intent of `boost::system::error_code`  
  
# Introduction  
  
Quoting from the Introduction page of boost.system:  
  
> Boost.System is part of the C++11 Standard Library. A number of changes, particularly to names, were made by the C++ committee during standardization. The Boost implementation has been tracking those changes.  
  
From this it seems reasonable to take descriptions from the boost documentation as canonical (since we are in the boost domain, and the boost implementation is tracking the intention of the standard).  
  
From the documentation from `boost::system::system_category()`:  
  
> The class error_code describes an object used to hold error code values, such as those **originating from the operating system** or other low-level application program interfaces.  
  
Now from the documentation of `boost::system::error_category`:  
  
> The class error_category defines the base class for types used to identify the source and encoding of a particular category of error code.  
  
> Classes may be derived from error_category to support categories of errors **in addition to those defined in Boost.System**.  
  
# Behaviour in ASIO  
  
It is noteworthy that ASIO does define its own error categories (although these are not documented) and does *sometimes* perform translations from `system_category` errors to asio domain errors.  
  
A very notable example, due to its importance in writing portable asynchronous applications, is the `error_code` created from `boost::asio::error::operation_aborted`. This 'error' is actually not an error at all. It is a legitimate notification that an asynchronous operation has been aborted as a result of specific actions by the user program. It may be that the means of discovering this action is from a system-sourced error code, but this is not the *origin* of the error. The origin of the error would be the call to `cancel()` or similar on the io object.  
  
Therefore, in this case, the asio library source code might appear to "translate" the system error. This is not actually the semantic intent.  
  
# Boost.Beast `file_body`  
  
When this class manipulates the underlying file object, the expectation is that the operation will succeed. Therefore the reporting of an error code from a file operation is truly an error "from the operating system". It is therefore reasonable to report this error as an `error_code` of category `system_category()`. There is no other domain-specific error that could reasonably be presented.  
  
# What if we wanted to report a domain-specific error?  
  
We would have to create a new (or extend the existing) error category within beast to represent "failures while touching the file in a file handle". We could provide a selection of meaningful error codes, such as "write failed" and provide translations from the (various) operating systems' error codes.  
  
## Problems with this approach  
  
### 1: We'd have to enumerate all possible system errors on all systems.  
  
This is (reasonably) trivial in a unix environment (but there are gotchas). It is much more difficult in Windows.  
  
Additionally, we don't know in advance all system variants for which beast will be compiled. Beast will support any system for which Asio can be made to work. Imagine how amazed I was when I got an asio-based program to run in a web browser by transpiling it with Emscripten.  
  
### 2: We'd lose information  
  
Unless we translated every single error code that windows could possibly generate, we'd end up  hiding information from a developer or user who might be keen to understand the cause of the problem.  
  
### 3: It gains us nothing  
  
Having provided translation tables and error descriptions for every error on every target, we would be in a position to convey, in the best possible case, no more information than was originally available in the error code of `system_error` category.  
  
We'd have spent a load of time on discovery, coding, documentation to yield no more information at the point of need. Furthermore, we would be duty bound to track all future updates of Windows, linux, bsd, etc error codes.  
  
# Summary  
  
I'm really pleased that you raised this issue as it gave me cause to examine a number of areas of the c++ standard and boost library in order to fully understand intent, rationale and correct use.  
  
This journey of discovery has led me to the view that the current implementation is correct in general.  
  
It would be reasonable to submit a specific issue if it is discovered that a `system_category` error were being reported when in fact the error originated somewhere else. For example:   
* a failed runtime precondition   
* an invalid argument  
* a cancelled operation  
* etc  
  
In the meantime, it is my view that a library code change is not warranted for this issue.  
  
I'm happy to hear any feedback to this response.  
  
Thank you for taking the time to read it.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-01-22 22:53:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577424405  

> It's probably a good idea to map these errors like ASIO does it   
  
Exactly which errors do you think we should map, and what should they be mapped to? Can you please enumerate them ?

---

## Comment 7

> Username: mika-fischer  
> Created at: 2020-01-23 09:23:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577596470  

Guys, let's go back to the [first message](https://github.com/boostorg/beast/issues/1815#issue-552878025) of this issue. Please do note the following:  
- The OS specific error *only* occurs due to beast directly using the `TransmitFile` win32 API call. This means that in all other cases, where beast just uses ASIO APIs, there is nothing to do.  
- Also note that the errors have nothing to do with file I/O, they are network errors.  
- In similar cases in ASIO (e.g. when directly calling `WSARecv`), ASIO translates the OS specific error codes to portable error codes (see the [link](https://github.com/boostorg/asio/blob/6534af41b471288091ae39f9ab801594189b6fc9/include/boost/asio/detail/impl/socket_ops.ipp#L842-L857) in the first message)  
- This makes it possible to write portable code using ASIO and react to certain error states (like "the connection was reset") without every application having to check against a plethora of platform specific error codes, which I consider very important.  
- I already listed the error codes we saw in the [first message](https://github.com/boostorg/beast/issues/1815#issue-552878025), but I'm not sure if they are the only ones. As a first step I would propose just doing [what ASIO does when it does `WSASend`](https://github.com/boostorg/asio/blob/6534af41b471288091ae39f9ab801594189b6fc9/include/boost/asio/detail/impl/socket_ops.ipp#L1231-L1247).  
  
I hope this clears things up.  
  
There are certain alternatives I see to translating the errors in beast:  
- Move `TransmitFile` and `sendfile` support from beast to asio. They are useful outside the http context and are so low-level that they fit better in asio IMO.  
- Change asio to not do the translation of `error_code`s. This would necessitate providing network `error_condition`s that can be used to compare the system specific `error_code`s to portable `error_condition`s, similar to [`std::errc`](https://en.cppreference.com/w/cpp/error/errc). In some ways this would be cleaner than what asio currently does, but sometimes translation is necessary (see e.g. [here](https://github.com/boostorg/asio/blob/b6c27b9c3167b9b7dd0a30de9dfe6f3aa33bdaf3/include/boost/asio/detail/win_iocp_socket_service_base.hpp#L64-L69) and [here](https://github.com/boostorg/asio/blob/6534af41b471288091ae39f9ab801594189b6fc9/include/boost/asio/detail/impl/socket_ops.ipp#L1236-L1242))

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-01-23 13:31:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577682232  

Can you please explain exactly what is the problem that you are experiencing? You are using `win32_file_body` and you get a Windows specific error code which should have been translated to a generic asio error? Which error code are you getting and in what situation?

---

## Comment 9

> Username: mika-fischer  
> Created at: 2020-01-23 14:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577711608  

The scenario is as follows:  
- I'm writing a HTTP client on Windows  
- I'm trying to re-use an existing connection for a subsequent HTTP request  
- The request has a `file_body` (i.e. it uses the `async_write_some` specialization for `file_win32`)  
- The connection was already closed by the peer, but I don't know that yet and try to send the request anyway  
- Then `async_write_some` sometimes fails with the following error codes:  
  - [ERROR_NETNAME_DELETED](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-erref/18d8fbe8-a967-4f1c-ae50-99ca8e491d2d) (this happened regularly)  
  - [WSAEINTR](https://docs.microsoft.com/en-us/windows/win32/winsock/windows-sockets-error-codes-2) (this happened only once)  
  - Other error codes which are part of `asio::errror` or `beast::http::error`  
- I just noticed this because I already have a whitelist of errors that I consider harmless (i.e. they are due to the attempted connection re-use) and consider everything else a hard failure.  
  
I'm pretty sure what happens is that I get the "normal" (i.e. `asio::error` / `beast::http::error`) errors as long as the error is raised during `async_write_some` of the header (which just uses `async_write_some` of the underlying stream) and I get the system-specific errors if the error is raised during `TransmitFile`.  
  
My suggestion is that the `async_write_some` from beast should behave similarly as the `async_write_some` of the underlying stream and ASIO streams will not return those error codes AFAICT.  
  
The whitelist is as follows:  
```cpp  
bool is_retryable_error(error_code ec) {  
    return retry_allowed_ && (ec == boost::asio::error::broken_pipe ||  
                              ec == boost::asio::error::connection_aborted ||  
                              ec == boost::asio::error::connection_reset ||  
                              ec == boost::asio::error::bad_descriptor ||  
                              ec == boost::asio::error::network_down ||  
                              ec == boost::asio::error::network_reset ||  
                              ec == boost::asio::error::not_connected ||  
                              ec == boost::asio::error::operation_aborted ||  
                              ec == boost::asio::error::shut_down ||  
                              ec == boost::beast::http::error::end_of_stream);  
}  
```  
  
Of course I've since worked around this issue by checking for these windows-specific error codes. But IMO, client code using beast should not need to handle platform-specific error codes in order to handle expected error conditions (such as connection reset).

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-01-23 15:10:15 UTC  
> Updated at: 2020-01-23 15:10:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-577723187  

@mika-fischer That's what I thought (probably should have included when the issue was first opened). I agree with you, and I think the right fix is for beast simply to remap the error. However, Richard Hodges (a new staff engineer at C++ Alliance who is studying to take over Beast) and Peter Dimov feel that asio's treatment of errors is deficient.  
  
Specifically, that asio should define `connection_reset` as an `error_condition` not an `error_code`, and perform the remapping there. I can't argue with that, but convincing Chris to change it might be an uphill battle. Other ideas were proposed such as adding `beast::condition::connection_reset` and others, to fix the defect in asio, but I don't like it at all because it means teaching everyone who uses Beast that they need to check for errors differently.  
  
If you are interested in reading the conversation or participating, feel free to join the `#beast` channel on the C++ Slack, sign up free here: http://slack.cpp.al

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-01-27 09:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-578653630  

This issue is resolved by PR 1821

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-01-27 13:12:29 UTC  
> Updated at: 2020-01-27 13:12:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-578739829  

If the commit message included `fix #1821` in the body it would be picked up in this issue (and close automatically too!)

---

## Comment 13

> Username: mika-fischer  
> Created at: 2020-01-28 16:56:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-579349425  

I don't think this is fixed?!

---

## Comment 14

> Username: madmongo1  
> Created at: 2020-01-28 17:21:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-579360821  

I think the overall effect is fixed isn’t it?  
If not please do raise a specific issue with code to demonstrate and I’ll fix it.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-01-28 17:30:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-579365022  

That's my fault, I didn't look closely enough at the merged branch. A function like `http::async_write_some`, which shares the same name as an Asio API, should of course have the same behavior. If Asio maps `ERROR_NETNAME_DELETED` to some other `error_code`, then we should too. The place for the "real" fix for this is not in beast, it is in asio. Beast does not contain a solution to the OP's stated problem.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-01-28 18:12:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-579382877  

Try this? https://github.com/vinniefalco/beast/commit/f48703ab57d152ab813867eef8635d3a211946f8

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-01-29 16:19:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1815#issuecomment-579836507  

Updated: https://github.com/vinniefalco/beast/commit/9e9cc6f5d3c5d343c11b5f87f8a1b4ee672b88e7
