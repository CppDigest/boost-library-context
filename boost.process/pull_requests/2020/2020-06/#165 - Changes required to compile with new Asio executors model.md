# #165 Changes required to compile with new Asio executors model [Merged]

> Username: chriskohlhoff  
> Created at: 2020-06-24 10:08:18 UTC  
> Updated at: 2020-06-27 15:04:33 UTC  
> Merged at: 2020-06-27 05:54:35 UTC  
> Closed at: 2020-06-27 05:54:35 UTC  
> Url: https://github.com/boostorg/process/pull/165  

Targeted at 1.74

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2020-06-27 06:43:39 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650507949  

That broke my windows build. If I only include `netfwd.hpp` the `any_io_executor` isn't defined. How can I solve that, where do the config macros come from?

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2020-06-27 06:46:19 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650508243  

FYI I haven't yet merged the `netfwd.hpp` change to the `master` branch of asio. Just waiting for a few more tests to cycle before I merge it.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2020-06-27 06:49:16 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650508670  

Oh that makes sense - currently running on the `develop` branch?

---

## Comment 4

> Username: chriskohlhoff  
> Created_at: 2020-06-27 06:54:19 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650509459  

Yep. If you're testing your windows build locally maybe you can check out asio on `develop` just to double check it's all ok. Otherwise, I will probably do the merge to asio `master` in 6-8 hours or so.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2020-06-27 07:18:07 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650513100  

Yeah, works now, please ping me once merged.

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2020-06-27 15:04:33 UTC  
> Url: https://github.com/boostorg/process/pull/165#issuecomment-650572361  

Done.

---
