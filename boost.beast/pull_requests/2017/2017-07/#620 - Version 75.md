# #620 Version 75 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-07 20:59:27 UTC  
> Updated at: 2017-07-08 14:59:22 UTC  
> Merged at: 2017-07-08 14:58:50 UTC  
> Closed at: 2017-07-08 14:58:50 UTC  
> Url: https://github.com/boostorg/beast/pull/620  

* Use file_body for valid requests, string_body otherwise.  
* Construct buffer_prefix_view in-place  
* Shrink serializer buffers using buffers_ref  
* Tidy up BEAST_NO_BIG_VARIANTS  
* Shrink serializer buffers using buffers_ref  
* Add serializer::limit  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v75

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-07 23:19:57 UTC  
> Updated_at: 2017-07-08 14:21:04 UTC  
> Url: https://github.com/boostorg/beast/pull/620#issuecomment-313814581  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=h1) Report  
> Merging [#620](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/6f88f0182d461e9cabe55032f966c9ca4f77bf46?src=pr&el=desc) will **increase** coverage by `0.06%`.  
> The diff coverage is `98.03%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/620/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #620      +/-   ##  
===========================================  
+ Coverage    93.45%   93.52%   +0.06%       
===========================================  
  Files          100      101       +1       
  Lines         7698     7718      +20       
===========================================  
+ Hits          7194     7218      +24       
+ Misses         504      500       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/file.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGUuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/file\_posix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGVfcG9zaXguaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/in\_place\_init.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9pbl9wbGFjZV9pbml0LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.59% <100%> (+0.09%)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/detail/buffers\_ref.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJzX3JlZi5ocHA=) | `100% <100%> (ø)` | |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.09% <100%> (+3.97%)` | :arrow_up: |  
| [include/beast/core/impl/buffer\_prefix.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyX3ByZWZpeC5pcHA=) | `95.94% <66.66%> (-4.06%)` | :arrow_down: |  
| ... and [4 more](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=footer). Last update [6f88f01...0e23066](https://codecov.io/gh/vinniefalco/Beast/pull/620?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: octopus-prime  
> Created_at: 2017-07-08 10:09:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/620#pullrequestreview-48753699  

SSE4.2 still does not work.

---

## Review 3 [Commented]

> Username: octopus-prime  
> Created_at: 2017-07-08 10:10:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/620#pullrequestreview-48753704  

I don't understand why the responses (string_response/file_response) must be member of http_worker instead of instantiating them when needed locally in the methods.

---

## Review 4 [Commented]

> Username: octopus-prime  
> Created_at: 2017-07-08 10:11:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/620#pullrequestreview-48753718  

crash  
gcc.link bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test  
*** Error in `bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test': double free or corruption (out): 0x00007ff7140008c0 ***  
======= Backtrace: =========  
/lib/x86_64-linux-gnu/libc.so.6(+0x7908b)[0x7ff71cbad08b]  
/lib/x86_64-linux-gnu/libc.so.6(+0x82c3a)[0x7ff71cbb6c3a]  
/lib/x86_64-linux-gnu/libc.so.6(cfree+0x4c)[0x7ff71cbbad2c]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0x253f7)[0x55a58dfec3f7]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0x25479)[0x55a58dfec479]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0x298ba)[0x55a58dff08ba]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0xa955b)[0x55a58e07055b]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0xa98b3)[0x55a58e0708b3]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0xa9f0a)[0x55a58e070f0a]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0x10523)[0x55a58dfd7523]  
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf1)[0x7ff71cb543f1]  
bin/test/server/gcc-7/release/debug-symbols-on/threading-multi/server-test(+0x1211a)[0x55a58dfd911a]

---

## Review 5 [Commented]

> Username: octopus-prime  
> Created_at: 2017-07-08 10:12:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/620#pullrequestreview-48753733  

I don't understand too much of serialized.ipp to review your changes. IMO it is too much switch/case - leading to fall-through warning.  
  
No chance to use some other technique here?  
What are e.g. do_body, do_body + 2, etc?  
Can they be types and be visited?  
Can they be states to use a state-machine?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-07-08 12:53:59 UTC  
> Url: https://github.com/boostorg/beast/pull/620#issuecomment-313854239  

>SSE4.2 still does not work.  
  
I don't know how to fix it correctly  
  
> I don't understand why the responses (string_response/file_response) must be member of http_worker   
  
Try running a benchmark of http-server-fast compared to some of the other servers. Its easily 35% to 60% faster. It might look strange but it helps performance.  
  
> double free or corruption (out): 0x00007ff7140008c0 ***  
  
No symbols? Did you try bisecting?  
  
>I don't understand too much of serialized.ipp...  
>No chance to use some other technique here?  
  
I don't think so. It is what it is, this was the most direct way to express the intent of the implementation. I need debug symbols are more information if I am going to figure out why it is crashing for you.  
  
>What are e.g. do_body, do_body + 2, etc?  
  
Those are the steps in the state machine for advancing the buffers and preparing the new buffers. Its all quite optimized.  
  
>leading to fall-through warning.  
  
I thought we fixed those warnings?

---

## Comment 7

> Username: octopus-prime  
> Created_at: 2017-07-08 13:18:34 UTC  
> Updated_at: 2017-07-08 13:18:56 UTC  
> Url: https://github.com/boostorg/beast/pull/620#issuecomment-313855471  

> I thought we fixed those warnings?  
  
I believe is gcc 7.0.1 bug. Nevertheless: no switch/case - no fall-through ;-)  
  
> Its easily 35% to 60% faster. It might look strange but it helps performance.  
  
That's incredible much. So okay - responses are members ;-)  
  
> I don't know how to fix it correctly  
  
I think the SSE4.2 pull request was good enough for the moment and could be refined later.  
So somebody must request a 3rd pull - rebased on a new branch. That makes contributors tired.  
  
> No symbols? Did you try bisecting?  
  
The problem here is to reproduce the crash. The test does not crash always. It's like a "defective contact". So may be it's something in multithreading...

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-07-08 13:29:54 UTC  
> Url: https://github.com/boostorg/beast/pull/620#issuecomment-313856003  

>I think the SSE4.2 pull request was good enough for the moment and could be refined later.  
  
I'll rebase it.

---
