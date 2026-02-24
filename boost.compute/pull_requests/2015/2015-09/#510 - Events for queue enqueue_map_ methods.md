# #510 Events for queue enqueue_map_ methods [Merged]

> Username: jszuppe  
> Created at: 2015-09-12 14:24:43 UTC  
> Updated at: 2016-05-01 11:19:27 UTC  
> Merged at: 2015-09-29 02:50:10 UTC  
> Closed at: 2015-09-29 02:50:10 UTC  
> Url: https://github.com/boostorg/compute/pull/510  

This addresses https://github.com/boostorg/compute/issues/365 (https://github.com/boostorg/compute/pull/496 only partiality fixed that issue). Now `enqueue_map_buffer()` returns associated event through non-const parameter `map_buffer_event` (there is also overload without this parameter).  
  
I also added missing enqueue_map_image method (with test in `test_image2d.cpp`) and asynchronous versions for enqueue_map_buffer(), enqueue_read_buffer_rect() and enqueue_write_buffer_rect().

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-09-12 17:26:35 UTC  
> Updated_at: 2015-09-12 17:50:36 UTC  
> Url: https://github.com/boostorg/compute/pull/510#issuecomment-139798930  

This https://github.com/boostorg/compute/issues/245 bug occurred in Travic-CI run with clang, but it's not connected to this PR (this bug also occurs on master - https://github.com/boostorg/compute/pull/511 should fix it).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-09-29 02:50:12 UTC  
> Url: https://github.com/boostorg/compute/pull/510#issuecomment-143928606  

Tested this out on my machines, seems to work well. Thanks!

---
