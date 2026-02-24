# #233 permessage-deflate [Merged]

> Username: vinniefalco  
> Created at: 2017-01-20 21:46:44 UTC  
> Updated at: 2017-01-28 01:13:49 UTC  
> Merged at: 2017-01-28 00:48:33 UTC  
> Closed at: 2017-01-28 00:48:33 UTC  
> Url: https://github.com/boostorg/beast/pull/233  

This branch fixes a number of bugs and issues with the earlier permessage-deflate implementation.   
  
These commits are new:  
  
* Add optional yield_to arguments  
* [FOLD] Refactoring and fixes

---

## Comment 1

> Username: codecov-io  
> Created_at: 2017-01-20 22:31:46 UTC  
> Updated_at: 2017-01-26 17:17:42 UTC  
> Url: https://github.com/boostorg/beast/pull/233#issuecomment-274197640  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/233?src=pr) is 94.76% (diff: 87.34%)  
> Merging [#233](https://codecov.io/gh/vinniefalco/Beast/pull/233?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **1.30%**  
  
```diff  
@@             master       #233   diff @@  
==========================================  
  Files            86         87     +1     
  Lines          5780       6229   +449     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           5553       5903   +350     
- Misses          227        326    +99     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [c00cd37...e92e275](https://codecov.io/gh/vinniefalco/Beast/compare/c00cd37b8a441a92755658014fdde97d515ec7ed...e92e275b35a7ab6f84a4646cfabaa03e1c4b13bd?src=pr)

---

## Review 2 [Commented]

> Username: miguelportilla  
> Created_at: 2017-01-21 00:38:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/233#pullrequestreview-17792248  

📁 include/beast/websocket/detail/invokable.hpp

```diff
 131 |         if(base_)
 132 |         {
 133 |             auto const basep = base_;
```

> Username: miguelportilla  
> Created_at: 2017-01-21 00:38:37 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97182564  

Can this local be avoided?  
`        if (! base_)  
            return false;  
        (*base_)();  
        base_ = nullptr;  
        return true;`

> Username: ximinez  
> Created_at: 2017-01-21 00:45:27 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97183077  

As written, this ensures that `base_` is null _before_ the function gets called. This prevents recursion / callback problems.

> Username: vinniefalco  
> Created_at: 2017-01-21 00:59:41 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184080  

Nope, because a call to `(*base_)()` could cause `emplace()` to get called which would break invariants. `base_` has to be set to `nullptr` before the invocation takes place.

> Username: vinniefalco  
> Created_at: 2017-01-21 01:02:12 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184255  

What he said


---

## Review 3 [Changes requested]

> Username: ximinez  
> Created_at: 2017-01-21 00:59:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/233#pullrequestreview-17779197  

📁 extras/beast/test/yield_to.hpp

```diff
  97 |+                 std::forward<Args>(args)...,
  98 |+                     std::placeholders::_1));
  99 |+     }
```

> Username: ximinez  
> Created_at: 2017-01-20 22:34:44 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97169639  

This overload is nifty, but I don't see them being called anywhere, so I rewrote some existing test calls, and they all still pass.  https://github.com/ximinez/Beast/commit/916d785fe8021bc22af434225498f6e41b82ae52

> Username: vinniefalco  
> Created_at: 2017-01-21 01:02:32 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184282  

Isn't it called from stream.cpp? I like your changes!

> Username: ximinez  
> Created_at: 2017-01-21 01:06:40 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184571  

The calls in stream.cpp both have one parameter - a lambda, so it calls the other override.

> Username: ximinez  
> Created_at: 2017-01-21 01:06:47 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184577  

PS. Thanks!


📁 test/websocket/stream.cpp

```diff
1044 |-             auto const restart =
1045 |-                 [&](error_code ev)
1190 |+                 if(! pmd.client_enable)
```

> Username: ximinez  
> Created_at: 2017-01-20 23:50:37 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97178328  

It may be helpful down the road to add a comment here explaining that these tests rely on forcing a frame to be emitted, which will (correctly) not happen when compression is on.

---

📁 test/websocket/stream.cpp

```diff
1313 |+         pmd.client_max_window_bits = 10;
1314 |+         pmd.client_no_context_takeover = true;
1315 |+         doClientTests(pmd);
```

> Username: ximinez  
> Created_at: 2017-01-21 00:01:18 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97179319  

`server_enable` defaults to false, and I don't see anywhere that it's getting enabled on the server, though I may have missed it. That means that deflate is not being tested by any config.  
  
The coverage report seems to support this hypothesis, because `inflate` and `deflate` are both red. https://codecov.io/gh/vinniefalco/Beast/src/83173f5578294b0e2c0bb03c2a6dd0328a1b2e8c/include/beast/websocket/detail/pmd_extension.hpp#L365...477

> Username: vinniefalco  
> Created_at: 2017-01-21 01:02:54 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97184328  

*whoops* that explains the drop in coverage


---

## Review 4 [Commented]

> Username: miguelportilla  
> Created_at: 2017-01-25 19:46:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/233#pullrequestreview-18486188  

📁 include/beast/websocket/detail/mask.hpp

```diff
  62 |     std::random_device rng;
  63 |+ #if 0
  64 |     std::array<std::uint32_t, 32> e;
```

> Username: miguelportilla  
> Created_at: 2017-01-25 19:46:09 UTC  
> Updated_at: 2017-01-28 00:48:05 UTC  
> Url: https://github.com/boostorg/beast/pull/233#discussion_r97862944  

This might work:  
  
    std::uint32_t e[32];  
    std::generate(std::begin(e), std::end(e), std::ref(rng));  
    std::seed_seq ss(std::begin(e), std::end(e));


---

## Review 5 [Approved]

> Username: ximinez  
> Created_at: 2017-01-26 20:24:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/233#pullrequestreview-18715049  

Updates look good. 👍

---

## Comment 6

> Username: miguelportilla  
> Created_at: 2017-01-26 23:26:33 UTC  
> Url: https://github.com/boostorg/beast/pull/233#issuecomment-275546336  

👍 Looks sane.

---
