# #201 - Question: a way to hide #include <x86intrin.h> from public headers? [Closed]

> Username: anarthal  
> Created at: 2024-05-29 19:24:13 UTC  
> Updated at: 2024-05-30 07:03:20 UTC  
> Closed at: 2024-05-30 07:03:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/201  

My build benchmark is flagging `boost/charconv/from_chars.hpp` (which I expected to be a lightweight header) as slow to include (taking ~1s). Looks like it's due to `#  include <x86intrin.h>`. Is it viable to avoid including this header in the public charconv headers?

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-30 06:11:19 UTC  
> Url: https://github.com/boostorg/charconv/issues/201#issuecomment-2138754124  

Yes; looks like I stopped using the intrinsics a while ago, but never removed the headers.
