# #2205 - Robust HTTP Server Example [Open]

> Username: vinniefalco  
> Created at: 2021-03-24 17:11:42 UTC  
> Updated at: 2026-01-29 23:13:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2205  

It would be nice to have a new example HTTP server that supports POST to upload files, GET / to see the list of files, and GET <file> to download a file.

---

## Comment 1

> Username: Tectu  
> Created at: 2021-03-26 09:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-808078267  

I am currently working on that...  
Not that anybody should necessarily wait for that. My experience working with beast is very limited at the moment.

---

## Comment 2

> Username: Tectu  
> Created at: 2021-03-30 00:15:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-809809725  

The current code (on my end) is based on C++20 (could be easily down-graded to C++17). Does this even make for a useful example for you guys?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-03-30 09:13:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-810057528  

Personally I think there is great value in a c++20 example.   
@vinniefalco I presume we'll also want to implement a c++11 version?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-04-07 15:27:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-815007279  

You have to use your best judgement. Try to follow the existing examples in terms of style. A C++20 example is okay (I assume because you want to use `co_await`). But in that case you also need a regular async/callback port of it. We can leave out the synchronous version :)

---

## Comment 5

> Username: Tectu  
> Created at: 2021-04-29 01:22:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-828882916  

Here is my "example" on how to use `boost.beast`: https://github.com/Tectu/malloy  
I might have digressed a bit.  
Maybe it's still worth listing it somewhere?  
  
This is so far mainly the grunt work. It has many rough edges and needs serious improvements in several places.  
  
I'd be thankful for any kind of feedback (or direct contributions!)

---

## Comment 6

> Username: Tectu  
> Created at: 2021-08-30 17:36:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908544962  

The "example" has been released as `v0.1.0`: https://github.com/Tectu/malloy  
  
Current feature list:  
  
- High-level controller to setup I/O context, SSL context, worker threads and more  
- HTTP  
  - Plain or TLS (SSL) connections  
  - Cookies  
  - Sessions  
  - Upgrading connections to WebSocket  
  - Client  
    - Response filters  
  - Server  
    - Routing  
      - Simple handlers (useful for building REST APIs)  
        - Target matching via regex  
        - Capturing groups via regex  
      - Sub-routers (nested/chained routers)  
      - Redirections  
      - File serving locations  
      - Preflight responses  
      - Access policies  
        - HTTP basic auth  
        - Custom access policies  
      - Websocket endpoints (with auto-upgrade from HTTP)  
    - Request filters  
- WebSocket  
  - Client  
  - Server  
  - Connections upgradable from HTTP  
- HTML  
  - Forms  
    - Supported encoding types  
      - `application/x-www-form-urlencoded`  
      - `multipart/form-data`  
      - `text/plain`  
    - Parsing  
    - Rendering  
  
Feedback and contributions are highly welcomed.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-08-30 17:38:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908546541  

This is BIG !!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-08-30 17:39:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908547179  

This is more like a framework than an example

---

## Comment 9

> Username: Tectu  
> Created at: 2021-08-30 17:43:32 UTC  
> Updated at: 2021-08-30 17:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908550290  

> This is more like a framework than an example  
  
Hence I wrote _"example"_ in quotation marks :p  
  
No but seriously. This honestly started off as an example - I kid you not. I had several application using `beast` and a lot of the stuff was being repeated between applications. Putting this into a library/framework made sense (to me).  
  
I will keep push this forward. I think that a fair share of `beast` users are dealing with lower level stuff they don't necessarily want to deal with but have to. I hope that this library will ease the pain and provide developers looking for a higher-level solution with a solid base built on top of `beast`.  
  
You can definitely help out with a star ;p

---

## Comment 10

> Username: vinniefalco  
> Created at: 2021-08-30 18:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908573504  

I was so caught up looking at the wonderful work you've gone that I forgot to Star, Like and smash that Subscribe button

---

## Comment 11

> Username: Tectu  
> Created at: 2021-08-30 18:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908589701  

Haha ^^  
  
So, do you think this is useful to you and other `beast` users? Should we point people towards this as an option for those who'd like to avoid the lower level stuff?

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-08-30 20:28:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908670595  

I’d like to write a little application with it to give you some feedback.

---

## Comment 13

> Username: Tectu  
> Created at: 2021-08-30 20:32:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-908672957  

Please do - that would be highly appreciated.

---

## Comment 14

> Username: Tectu  
> Created at: 2022-01-05 19:33:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-1006017960  

New release is available: https://github.com/Tectu/malloy/releases/tag/0.3.0

---

## Comment 15

> Username: Tectu  
> Created at: 2022-01-07 14:32:43 UTC  
> Updated at: 2022-01-07 14:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-1007450835  

Seems like we're on the right track here, guys!  
Some feedback from a [user & awesome contributor](https://github.com/Tectu/malloy/issues/103#issuecomment-1007443368) of malloy:  
  
> Serving a file with load balanced across multiple threads and asynchronous in less than 50 lines is damn impressive (its one of the reasons I started using and contributing to malloy, the router and client interfaces are really nice well done :p)  
  
I think this approach of `boost.beast` being low level and `malloy` being higher level is gonna work out in everyone's favor!  
As such: Please keep up the good work guys!

---

## Comment 16

> Username: madmongo1  
> Created at: 2022-01-07 14:50:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-1007466923  

Wonderful.

---

## Comment 17

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:26:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-1256866798  

@Tectu any progress to report?

---

## Comment 18

> Username: Tectu  
> Created at: 2022-09-24 10:44:57 UTC  
> Updated at: 2022-09-24 12:26:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-1256936729  

> @Tectu any progress to report?  
  
Yeah, lots of progress to report - anything specific you're interested in?  
I didn't report back here on each release as I didn't want to spam - but library developments are going well :)  
  
The next release of malloy (`0.6.0`) is expected to land in the next few days.  
And probably most importantly: By now, malloy is being used successfully in a couple of real-world programs/software.  
There is also ongoing work for providing some higher-level interface to create REST APIs in the `feature/rest` branch. Hopefully, GraphQL will follow in the future as well. Of course, all of these things are optional to keep the core library as lean as possible.  
  
Malloy is now also available as a package on FreeBSD. If somebody feels like creating packages for other operating systems (such as Ubuntu, Fedora, Arch, MacOS ...) that would be great :)

---

## Comment 19

> Username: pinballcutie1992  
> Created at: 2026-01-28 18:04:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3812942207  

No plans to add part of malloy in example? Form handling would be super helpful

---

## Comment 20

> Username: vinniefalco  
> Created at: 2026-01-29 00:51:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3814722042  

Sorry, who is Malloy?

---

## Comment 21

> Username: Tectu  
> Created at: 2026-01-29 13:12:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3817612814  

> Sorry, who is Malloy?  
  
I assume they are talking about https://github.com/tectu/malloy   
Not sure what their actual question is tho.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2026-01-29 18:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3819649506  

Beast2 is in development:  
  
https://github.com/cppalliance/beast2  
https://github.com/cppalliance/http  
https://github.com/cppalliance/corosio  
https://github.com/cppalliance/capy

---

## Comment 23

> Username: pinballcutie1992  
> Created at: 2026-01-29 19:12:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3819746922  

I meant using malloy as base for http server example. Beast2 is stunning news! Any announce with more info about it? Why is it beast2 and not just new version of beast, is it replacement for current beast or parallel high level lib?

---

## Comment 24

> Username: vinniefalco  
> Created at: 2026-01-29 23:13:07 UTC  
> Updated at: 2026-01-29 23:13:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2205#issuecomment-3820881033  

# Boost.Beast2: Coroutines-Only Networking for Modern C++  
  
## The Problem  
  
Boost.Asio transformed C++ networking. It also burdened every call site with templates, platform headers, and implementation machinery, yet its quarter century of field experience produced capable, working abstractions.  
  
The C++26 `std::execution` API offers a different model, designed to support heterogenous computing. Our research indicates it optimizes for the wrong constraints: TCP servers don't run on GPUs. Networking demands zero-allocation steady-state, type erasure without indirection, and ABI stability across (e.g.) SSL implementations. C++26 delivers things that networking doesn't need, and none of the things that networking does need.  
  
## The Design Principle  
  
Peter Dimov insists we begin with what developers actually write:  
  
```cpp  
auto [ec, n] = co_await sock.read_some( buf );  
```  
  
One line. No platform headers. No completion handlers. Just coroutines. This simplicity drives every architectural decision:  
  
- **Clean call sites** — no platform or implementation headers leak through  
- **Extensible concepts** — user-defined buffer sequences and executors; stream wrappers without templates  
- **Zero-alloc steady state** — no coroutine frame allocations in hot paths  
- **Minimal template exposure** — implementation details stay in the translation unit  
- **Allocation-free type erasure** — user types preserved without small buffers or malloc  
- **Fine-grained execution control** — strands, processor affinity, priority scheduling  
  
Boost.Asio gave us the field experience. These design choices reflect what we learned.  
  
## The Library Family  
  
Beast2 is an umbrella term for a family of libraries that work together:  
  
**Boost.Capy** — The execution foundation, optimized for async I/O. Core types: `task<T>`, `thread_pool`, `strand`, `async_mutex`. Tasks propagate executor context through `await_suspend` automatically, guaranteeing correct executor affinity. Provides allocation-free type erasure, frame allocators for coroutine memory, automatic stop token propagation, and buffer algorithms. **This should go into the C++ Standard Library**.  
  
**Boost.Corosio** — Coroutine-first portable networking and I/O that wrap per-platform implementations. Every operation returns an awaitable; no callbacks. Core types: `socket`, `acceptor`, `resolver`, `strand`, `io_context`, SSL stream wrappers (WolfSSL and OpenSSL), non-templated streams, scatter/gather buffers, native `std::stop_token` cancellation. This **should not** go into the C++ Standard Library, for a while.  
  
**Boost.Http** — Sans-I/O HTTP/1.1 with strict RFC compliance. Protocol logic isolated from I/O for testability and framework independence. Core types: `request`, `response`, `parser`, `serializer`, `router`. Non-templated containers, memory-bounded parsers, body streaming, Express.js-style routing, automatic gzip/deflate/brotli application, and an extensive collection of HTTP "middleware" algorithms such as multipart/form processing, cookie management, bcrypt, and more.  
  
**Boost.Websocket** — WebSocket algorithms and structures. Same philosophy.  
  
**Boost.Beast2** — High-level HTTP and WebSocket servers. Express.js-style routing, multithreaded, idiomatic C++.  
  
**Boost.Burl** — High-level HTTP client. The features of curl, the ergonomics of coroutines, and the design sensibility of Python Requests.  
  
Each of these libraries is built for direct use. Boost.Capy can be standardized without risky socket or SSL decisions. Combine as needed.  
  
## Technical Advantages  
  
Beast2 continues the Boost tradition of innovation through working code.  
  
### Zero-Allocation Steady-State  
  
`capy::task` coroutine frames reuse memory (delete before dispatch). Type erasure is achieved without small-buffer optimizations or malloc. User types are preserved through the abstraction boundary. Key insight:  
  
> _The coroutine frame allocation we cannot avoid, pays for all the type-erasure we need._  
  
### Templates Where They Matter  
  
Boost.Asio:  
```cpp  
template< class AsyncStream, class CompletionToken >  
auto do_io( AsyncStream&, CompletionToken&& );  
```  
  
Boost.Corosio:  
```cpp  
auto do_io( corosio::io_stream& ); // returns awaitable  
```  
  
No loss of generality. Buffer sequences and executors remain concept-driven. Stream wrappers require no templates. The drawbacks of `std::execution` can be seen in a single line:  
  
```cpp  
connect( sndr, rcvr ); // C++26, unavoidable templates  
```  
  
Here, the compiler must see all the types (or perform expensive type-erasure, which is inconvenient at this call site and uncompetitive). No encapsulation. Long compile times. ABI fragility. Unfriendly to network programs.  
  
### ABI Stability by Design  
  
Boost.Asio:  
```cpp  
template< class AsyncStream >  
class ssl_stream  
{  
    AsyncStream stream_;  
    ...  
```  
  
Boost.Corosio:  
```cpp  
class wolfssl_stream : public io_stream  
{  
    io_stream& wrapped_stream_;  
    ...  
```  
  
Stream algorithms see `io_stream&` not `wolfssl_stream&` and can be written as ordinary functions (non-templates). Link your HTTP library against Corosio and WolfSSL. Relink against OpenSSL. No recompilation. SSL implementation becomes a runtime decision with zero ABI consequence. Have both in the same executable if you want (maybe useful for researchers).  
  
### No Configuration Macros  
  
There is only one configuration of the library:  
  
Boost.Asio:  
```cpp  
#define BOOST_ASIO_NO_SCHEDULER_MUTEX 1  
  
{  
    asio::io_context ioc;  
}  
```  
  
Boost.Corosio:  
```cpp  
{  
    corosio::unsafe_io_context uioc; // no macro needed  
  
    corosio::io_context ioc; // use one or both, linker removes whats not used  
}  
```  
  
One library. One object file. Runtime configuration.  
  
### Implementation Hiding  
  
No platform headers at call sites. No implementation structures in user code. Translation-unit isolation by default. ABI is stable by design, and thus if adopted in the standard can be evolved rather than freezing.  
  
## Boost.Beast2  
  
Unlike its predecessor, Beast2 is _full-featured_ and **high-level**. Everything is in scope:  
  
Boost.Beast2:  
```cpp  
srv.wwwroot.use( "/", serve_static( "C:\\Users\\Vinnie\\my-website" ) );  
```  
  
Express.js patterns. Multithreaded execution. C++ performance.  
  
## Summary  
  
The Beast2 family of libraries responds to the pain points related by users and delivers everything they want. It demonstrates through working code what modern C++ networking requires: coroutine-native design, fast compilation without loss of generality, ABI stability across implementations, and good performance, while preserving every capability that made Boost.Asio great.  
  
This is the future of C++ networking.  
  
---  
  
*Boost.Beast2 is under active development. API subject to change.*
