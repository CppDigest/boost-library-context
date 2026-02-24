# #427 Add missing include [Closed]

> Username: camolezi  
> Created at: 2020-08-27 14:26:10 UTC  
> Updated at: 2020-09-02 19:06:07 UTC  
> Closed at: 2020-09-02 19:06:07 UTC  
> Url: https://github.com/boostorg/math/pull/427  

This patch makes the header able to be built standalone, making possible C++ clang modules builds.@vgvassilev

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-09-02 14:21:25 UTC  
> Url: https://github.com/boostorg/math/pull/427#issuecomment-685769513  

I admit to not understanding what is going on.  
  
In `boost/math/tools/atomic.hpp`, we `#include <atomic>` if it exists.  
  
Apparently this doesn't play nice with clang module builds?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-09-02 17:41:57 UTC  
> Url: https://github.com/boostorg/math/pull/427#issuecomment-685893190  

Exactly, all the correct headers are included at the proper times: note that we support situations where there is no threading support and no <atomic> (even though we may be in C++11 or later).  I also wonder how stable modules are yet?

---

## Comment 3

> Username: camolezi  
> Created_at: 2020-09-02 19:06:07 UTC  
> Url: https://github.com/boostorg/math/pull/427#issuecomment-685938562  

Hello, thanks for the reviews and insights.  
Sorry, this was a mistake on my part, we are actually excluding this file on our module map. So I'm closing this PR.

---
