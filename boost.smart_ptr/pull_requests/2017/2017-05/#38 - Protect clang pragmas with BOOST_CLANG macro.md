# #38 Protect clang pragmas with BOOST_CLANG macro. [Merged]

> Username: Belcourt  
> Created at: 2017-05-26 18:40:05 UTC  
> Updated at: 2017-05-27 00:03:55 UTC  
> Merged at: 2017-05-27 00:03:55 UTC  
> Closed at: 2017-05-27 00:03:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/38  

Building with Intel c++ on Mac generates numerous warnings of this flavor:  
  
In file included from /Users/kbelco/Projects/local/boost/include/boost/smart_ptr/detail/shared_count.hpp(28),  
                 from /Users/kbelco/Projects/local/boost/include/boost/smart_ptr/shared_ptr.hpp(28),  
                 from /Users/kbelco/Projects/local/boost/include/boost/shared_ptr.hpp(17),  
                 from /Users/kbelco/Projects/edr/src/edr/json_spirit_v4.08/json_spirit/json_spirit_value.h(21),  
                 from /Users/kbelco/Projects/edr/src/edr/json_spirit_v4.08/json_spirit/json_spirit_writer.h(13),  
                 from /Users/kbelco/Projects/edr/src/edr/json_spirit_v4.08/json_spirit/json_spirit_writer.cpp(6):  
/Users/kbelco/Projects/local/boost/include/boost/smart_ptr/bad_weak_ptr.hpp(57): warning #161: unrecognized #pragma  
  # pragma clang diagnostic pop  
           ^  
This patch silences the warnings.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-05-26 23:18:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/38#issuecomment-304407039  

A bit odd for Intel to define `__clang__` without also implementing the `#pragma` but what can one do.

---

## Comment 2

> Username: Belcourt  
> Created_at: 2017-05-26 23:27:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/38#issuecomment-304408100  

Yes, odd.  But it's with this version of Intel C++:  
  
s988329:~ kbelco$ icpc --version  
icpc (ICC) 17.0.4 20170411  
Copyright (C) 1985-2017 Intel Corporation.  All rights reserved.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-05-26 23:40:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/38#issuecomment-304409421  

We can remove the `#ifdef __clang__` if we test `BOOST_CLANG`, I suppose. I'll do that.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2017-05-26 23:43:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/38#issuecomment-304409647  

Works for me, thanks!

---
