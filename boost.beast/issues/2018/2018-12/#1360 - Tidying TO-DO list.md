# #1360 - Tidying TO-DO list [Closed]

> Username: vinniefalco  
> Created at: 2018-12-09 19:13:06 UTC  
> Updated at: 2022-06-16 21:31:09 UTC  
> Closed at: 2022-06-16 16:23:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1360  

List of things to do after tidying up:  
  
* move file traits to file.hpp  
* consider breaking up core/type_traits.hpp  
* Doxygen macros for ConstBufferSequence, MutableBufferSequence, and other concepts  
* Spell out vinnie.falco@gmail.com in headers  
* Check convenience headers for missing items  
* Use `__see_below__`  where needed  
* Support `std::ref` in `buffers_suffix`  
* Make sure custom iterators are all _DefaultConstructible_ and compare equal to `end()`  
* Replace `ec.assign(0, ec.category())` with `ec = {}`  
* Rename file_base.hpp to file_mode.hpp (API Change)  
* Document `session_alloc` and move to _experimental  
* Maybe deprecate `read_size`?  
* Ditch `string_param`  
* stream_traits.hpp ?  
* buffer_traits.hpp ?  
* Core comes first in quick reference  
* Websocket stream async op docs define destruction while ops are pending as undefined (and the destructor too)  
* Rename buffers.cpp to detail_buffers.cpp in core tests  
* Fix metafunctions, since the XSL doesn't process type aliases correctly  
  
Done:  
* Fix behavior of default constructed iterators yet again  
* add `buffers_iterator_type` (was `buffer_sequence_iterator`)  
* add `buffers_type` (common buffer type)  
  - use in buffers_prefix, et. al.  
* Consistent spelling for adaptor (e.g. `buffers_adapter`)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-09 19:19:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-445563547  

Documentation:  
* working with buffers  
  - buffers adaptors (buffers_cat, buffers_prefix, buffers_suffix, buffers_range)  
  - dynamic buffers  
    * tutorial, diagram  
    * using dynamic buffers

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-01-13 23:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-453877601  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-01-21 07:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-455968935  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 4

> Username: simmse  
> Created at: 2020-03-24 15:34:41 UTC  
> Updated at: 2020-03-24 15:56:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-603310768  

Hello Everyone,  
  
Note this new finding with the Intel C++ 19.1 (e.g. Intel Parallel Studio XE 2020), integrated with the Visual Studio 2019, 16.4.6 and the buffers_prefix.hpp.  When set to build as a Console C++ executable, Debug | x86, you may observe a compiler access violation at buffers_prefix.hpp, line 280.  The complete message looks like this:  
  
```  
1>  C:\Boost\include\boost-1_72\boost/beast/core/impl/buffers_prefix.hpp(280): error : access violation  
1>            : buffers_prefix_view(size,  
1>                                 ^  
1>  
1>  compilation aborted for BeastTest.cpp (code 4)  
```  
  
The root cause is the default value for the Conformance mode option set to **Yes** (e.g. `/permissive-`).  It is under the Configuration Properties | C/C+ | Language | Conformance mode.  Setting this flag to **No** resolves the compiler access violation.  If using the command line, remove the `/permissive-` option.  I have only had this issue with Intel 19.1 C++ and Debug | x86.  All other Intel 19.1 C++ Configuration, Platform combinations successfully compile with the Conformance mode set to **Yes**.    
  
Within the Visual Studio 2019, 16.4.6, C++ compiler, the Debug | x86 combination compiles without an access violation, for either value of the Conformance mode option.  
  
Since there have been other issues with the /permissive- flag, perhaps a variation of the header file include list resolves it for the Intel C++ compiler?  
  
The above compiler access violation can also be observed by having one include of `beast.hpp` in a `.cpp`, then compile with Debug | x86, Conformance mode enabled (e.g. `/permissive-`).  When I quickly tested including only the buffers_prefix.hpp, it is too deep in the Beast hierarchy.  It would not compile nor produce the compiler access violation.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-03-24 16:31:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-603349698  

Why isn't this in its own issue?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-16 16:23:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-1157873025  

We aren't doing this anymore

---

## Comment 7

> Username: sehe  
> Created at: 2022-06-16 21:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1360#issuecomment-1158154647  

In fairness everything on the list that I could recognize has been completed, so kudos for a job well-done. And good to see the backlog get into shape. I'm keeping a close eye to pickup any loose ends that should not be dropped, but am already appreciating the tidying up that's happening.
