# #345 Version 39 [Merged]

> Username: vinniefalco  
> Created at: 2017-05-08 00:19:18 UTC  
> Updated at: 2017-05-10 02:28:14 UTC  
> Merged at: 2017-05-10 02:21:46 UTC  
> Closed at: 2017-05-10 02:21:46 UTC  
> Url: https://github.com/boostorg/beast/pull/345  

Beast versions are now identified by a single integer which is incremented on each merge. The macro BEAST_VERSION identifies the version number, currently at 39. A version setting commit will always be at the tip of the master and develop branches.  
  
  
* Use beast::string_view alias  
  
HTTP  
  
* Tidy up basic_parser javadocs  
  
WebSocket:  
  
* Add websocket async echo ssl server test:  
* Fix eof error on ssl::stream shutdown  
  
API Changes:  
  
* Refactor http::header contents  
* New ostream() returns dynamic buffer output stream  
* New buffers() replaces to_string()  
* Rename to multi_buffer, basic_multi_buffer  
* Rename to flat_buffer, basic_flat_buffer  
* Rename to static_buffer, static_buffer_n  
* Rename to buffered_read_stream  
* Harmonize concepts and identifiers with net-ts  
* Tidy up HTTP reason_string

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-08 03:01:53 UTC  
> Updated_at: 2017-05-10 01:28:57 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299761823  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=h1) Report  
> Merging [#345](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/dd02097d265857c3cfca435317f06f7af701a012?src=pr&el=desc) will **increase** coverage by `0.06%`.  
> The diff coverage is `97.93%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/345/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #345      +/-   ##  
==========================================  
+ Coverage   93.28%   93.34%   +0.06%       
==========================================  
  Files          95       93       -2       
  Lines        6503     6510       +7       
==========================================  
+ Hits         6066     6077      +11       
+ Misses        437      433       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/basic\_parsed\_list.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZWRfbGlzdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `87.5% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/hybi13.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2h5YmkxMy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.14% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ssl.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaXBw) | `100% <ø> (+100%)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3JmYzcyMzAuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/multi\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | |  
| ... and [58 more](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=footer). Last update [dd02097...f888136](https://codecov.io/gh/vinniefalco/Beast/pull/345?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vchang-akamai  
> Created_at: 2017-05-08 18:48:12 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299956164  

I tried building with b39. (gcc 4.8.4 on linux)  After fixing some API changes, I still ran into some compilation problems which appear to be related to the ostream_buffer changes.   
  
Excerpts:  
```  
/usr/include/c++/4.8/streambuf:802:7: error: 'std::basic_streambuf<_CharT, _Traits>::basic_streambuf(const std::basic_streambuf<_CharT, _Traits>&) [with _CharT = char; _Traits = std::char_traits<char>]' is private  
       basic_streambuf(const basic_streambuf& __sb)  
       ^  
  
[...]/include/beast/core/detail/ostream.hpp:72:5: error: within this context  
     ostream_buffer(  
  
  
[...]/include/beast/core/detail/ostream.hpp:203:33: note: synthesized method 'beast::detail::ostream_buffer<DynamicBuffer, CharT, Traits>::ostream_buffer(beast::detail::ostream_buffer<DynamicBuffer, CharT, Traits>&&) [with DynamicBuffer = beast::basic_multi_buffer<std::allocator<char> >; CharT = char; Traits = std::char_traits<char>]' first required here   
     , osb_(std::move(other.osb_))  
                                 ^  
```  
  
  
/usr/include/c++/4.8/streambuf:802:  
  
```  
    private:  
      // _GLIBCXX_RESOLVE_LIB_DEFECTS  
      // Side effect of DR 50.   
      basic_streambuf(const basic_streambuf& __sb)  
      : _M_in_beg(__sb._M_in_beg), _M_in_cur(__sb._M_in_cur),   
      _M_in_end(__sb._M_in_end), _M_out_beg(__sb._M_out_beg),   
      _M_out_cur(__sb._M_out_cur), _M_out_end(__sb._M_out_cur),  
      _M_buf_locale(__sb._M_buf_locale)   
      { }  
```  
Earlier in the same file, there is a protected constructor, but it's not a copy-constructor:  
```  
    protected:  
      /**  
       *  @brief  Base constructor.  
       *  
       *  Only called from derived constructors, and sets up all the  
       *  buffer data to zero, including the pointers described in the  
       *  basic_streambuf class description.  Note that, as a result,  
       *  - the class starts with no read nor write positions available,  
       *  - this is not an error  
      */  
      basic_streambuf()  
      : _M_in_beg(0), _M_in_cur(0), _M_in_end(0),   
      _M_out_beg(0), _M_out_cur(0), _M_out_end(0),  
      _M_buf_locale(locale())   
      { }  
```

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-05-08 19:42:53 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299969932  

Is this relevant?  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=54316

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-05-08 20:35:11 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299983017  

@vchang-akamai Does this reproduce the issue?  
https://wandbox.org/permlink/50w8X8rKnNbuhf6K

---

## Comment 5

> Username: vchang-akamai  
> Created_at: 2017-05-08 21:03:14 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299990146  

The build errors in your wandbox link appear to be pretty similar.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-05-08 21:17:12 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299993563  

I have an idea for a fix but it requires a memory allocation. You might not like it, but I don't see any way around it because GCC4.8.4 does not have full support for C++11 (which requires that `std::basic_streambuf` is movable). If the memory allocation bothers you, you will have to use a more recent GCC.

---

## Comment 7

> Username: vchang-akamai  
> Created_at: 2017-05-08 21:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299999345  

hmm.. ok. I may have to live with the memory allocation for now until we can update  our toolchain...

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-05-08 21:42:17 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-299999367  

@vchang-akamai This branch might fix the problem  
https://github.com/vinniefalco/Beast/commits/ostream  
  
I have not tested it on CI yet. Also, I need help figuring out what macro to check to determine if the version of libstdc++ being used does not support `std::basic_stream`.

---

## Comment 9

> Username: vchang-akamai  
> Created_at: 2017-05-08 21:49:36 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300000969  

I successfully built using the "ostream" branch.

---

## Comment 10

> Username: vchang-akamai  
> Created_at: 2017-05-08 22:22:47 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300007760  

Re: Macros:  
  
I'm not sure if this helps, but I found some GNU-specific libstdc++ macro info:  
[https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_macros.html](url)  
  
and a timeline of releases here:  
[https://gcc.gnu.org/develop.html#timeline](url)

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-05-08 22:50:05 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300012703  

@vchang-akamai Please try the **ostream** branch again, it uses `std::is_move_constructible` to switch between implementations as needed. It should work for you.

---

## Comment 12

> Username: vchang-akamai  
> Created_at: 2017-05-08 22:57:58 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300014074  

Building with new "ostream" branch does not work. Ran into same original problem with ostream_buffer and std::basic_streambuf.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2017-05-08 23:09:53 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300016007  

@vchang-akamai Hmm... I'm not sure why I am getting different results. On wandbox I have gcc4.8.4 set, and I even put a static assert in there that the `ostream_buffer` is not move constructible. Why is this code giving you different results from https://wandbox.org/permlink/iT3GPEn5fjaY4yLu ?  
I can't reproduce your compile error.

---

## Comment 14

> Username: vchang-akamai  
> Created_at: 2017-05-08 23:38:12 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300020317  

My code isn't actually calling ostream() on a multi_buffer.  It was my call to beast::websocket::async_accept() that seemed to trigger the error (there were first a bunch of messages about "required from" template functions in write.ipp and accept.ipp before it got to the ostream and std::basic_streambuf errors).  
  
I experimentally commented out my call to async_accept(), and then the compiler had a similar complaint for a call to beast::websocket::async_write()  
  
not sure if that clarifies anything...

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2017-05-08 23:42:02 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300020874  

Right, your code doesn't have to call `ostream`, it already happens in the implementation of Beast. Have you tried compiling the tests? I'd like to know if they build for you in the **ostream** branch.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2017-05-08 23:53:46 UTC  
> Updated_at: 2017-05-09 00:04:34 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300022589  

@vchang-akamai I need the smallest possible test case that reproduces the problem, could you please put that together on wandbox? I'd also like to know what version of stdlib you are using, and what happens when you compile this code in your project:  
```  
#include <beast/core/ostream.hpp>  
#include <beast/core/multi_buffer.hpp>  
static_assert(! std::is_move_constructible<beast::detail::ostream_buffer<  
    beast::multi_buffer, char, std::char_traits<char>>>::value, "");  
```

---

## Comment 17

> Username: vchang-akamai  
> Created_at: 2017-05-09 00:11:38 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300025242  

Trying to compile the beast tests:  My build environment's cmake is too old, so just compiling by hand from the "test" subdir.  
` g++ -m64 -std=c++11   -I [...]include -I../extras *cpp ../extras/beast/unit_test/main.cpp -L [...]/lib/ -lboost_system -o testsuite`  
(the "include" dir has both beast and boost. The "lib" dir has boost libs)  
  
I'm getting this error:  
```  
../extras/beast/unit_test/main.cpp:102:26: error: invalid initialization of non-const reference of type 'beast::unit_test::dstream {aka std::basic_ostream<char>&}' from an rvalue of type '<brace-enclosed initializer list>'  
     dstream log{std::cerr};  
                          ^  
```  
  
  
Ok, I'll see what I can do about producing a small wandbox example...

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2017-05-09 00:18:23 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300026203  

@vchang-akamai It looks like you're using a version of gcc with a bug in C++11 conformance:  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=50025

---

## Comment 19

> Username: vinniefalco  
> Created_at: 2017-05-09 00:51:18 UTC  
> Updated_at: 2017-05-09 00:51:24 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300030579  

@vchang-akamai I think this reproduces the issue, working on a fix:  
https://travis-ci.org/vinniefalco/Beast/jobs/230171812

---

## Comment 20

> Username: vchang-akamai  
> Created_at: 2017-05-09 00:55:26 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300031095  

Re: stdlibc++ version:  
I think I'm using GLIBCXX_3.4.19, dated 20150426  
  
Re: trying to compile the static_assert()  
I added it to one of my files and the static assertion failed.

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2017-05-09 03:34:51 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300052333  

@vchang-akamai Phew, I think this is fixed in the pull request!!

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2017-05-09 03:47:16 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300053787  

@vchang-akamai I also fixed the compilation error with the unit test (`dstream dout{std::cerr}`) so you should be able to build the unit tests now as well. Please let me know if there are still errors, thanks!

---

## Comment 23

> Username: vchang-akamai  
> Created_at: 2017-05-09 21:56:27 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300313510  

ok, I grabbed the latest b39 and it builds. Also tested the dstream error from the unit test and that's gone too.  
  
Thanks!!  
  
Q: Re: the memory allocation in your work around:   When does it occur?  Is this only if I use ostream on a multi_buffer?  I'm not currently doing that.  Or would it also happen if I use the various beast::websocket::async_*() calls?

---

## Comment 24

> Username: vinniefalco  
> Created_at: 2017-05-09 22:02:53 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300314912  

>Q: Re: the memory allocation in your work around: When does it occur? Is this only if I use ostream on a multi_buffer? I'm not currently doing that. Or would it also happen if I use the various beast::websocket::async_*() calls?  
  
It will happen any time Beast serializes the header of an HTTP message. This happens on `write` and `async_write`.

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2017-05-09 22:10:00 UTC  
> Url: https://github.com/boostorg/beast/pull/345#issuecomment-300316375  

If it makes you feel any better, the previous version was doing multiple memory allocations, this does just two :)

---
