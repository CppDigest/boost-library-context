# #81 Refactor monotonic_resource [Closed]

> Username: sdkrystian  
> Created at: 2020-04-27 00:32:33 UTC  
> Updated at: 2020-06-08 19:25:12 UTC  
> Closed at: 2020-05-14 02:04:05 UTC  
> Url: https://github.com/boostorg/json/pull/81  

Resolves #78

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-27 00:51:29 UTC  
> Updated_at: 2020-05-01 18:11:00 UTC  
> Url: https://github.com/boostorg/json/pull/81#issuecomment-619653703  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=h1) Report  
> Merging [#81](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/137c1653a8a8a7bb3b987ffa30f3ae4b0ac58f04&el=desc) will **increase** coverage by `0.12%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/81/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #81      +/-   ##  
===========================================  
+ Coverage    98.95%   99.08%   +0.12%       
===========================================  
  Files           57       58       +1       
  Lines         5092     5114      +22       
===========================================  
+ Hits          5039     5067      +28       
+ Misses          53       47       -6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/monotonic\_resource.ipp](https://codecov.io/gh/CPPAlliance/json/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbW9ub3RvbmljX3Jlc291cmNlLmlwcA==) | `97.01% <100.00%> (+12.70%)` | :arrow_up: |  
| [include/boost/json/monotonic\_resource.hpp](https://codecov.io/gh/CPPAlliance/json/pull/81/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL21vbm90b25pY19yZXNvdXJjZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=footer). Last update [137c165...d6b662c](https://codecov.io/gh/CPPAlliance/json/pull/81?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-04-27 01:37:34 UTC  
> Url: https://github.com/boostorg/json/pull/81#issuecomment-619663026  

The commit message is too long, since this is pre-release we can just make it "Refactor monotonic_resource."

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-27 01:40:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-400580976  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  83 |-     : block_size_(64 * 1024)
 100 |+ monotonic_resource(
 101 |+     std::size_t block_size,
```

> Username: vinniefalco  
> Created_at: 2020-04-27 01:40:02 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r415454987  

I think "block_size" is the wrong parameter. We should track the standard here. Instead of block_size we should make it `initial_size` and let that be the size of the first block allocated. And we should switch around the parameters, `(max_alloc, initial_size)` and provide defaults for both of them, with `initial_size` defaulting to zero (meaning, to use some internal constant). Again we should track the standard here, and the standard doesn't even have `initial_size` so there's that.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-27 01:40:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-400581134  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  89 |-     std::size_t block_size) noexcept
  90 |-     : block_size_(block_size)
 110 |+   void* initial_block,
```

> Username: vinniefalco  
> Created_at: 2020-04-27 01:40:37 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r415455155  

This parameter has the wrong name, it isn't a "block" in the sense of being prefixed with a `block` in the layout


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:01:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408785712  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  37 |-         std::size_t align) noexcept
  34 |+     std::uintptr_t
  35 |+     base_ptr() const
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:01:16 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422706457  

or `data() noexcept const`

> Username: sdkrystian  
> Created_at: 2020-05-10 23:40:19 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422717694  

Is there a point to doing this? It's implementation detail. `data()` usually implies that you get a pointer; this returns an integer.

> Username: vinniefalco  
> Created_at: 2020-05-10 23:44:45 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422718257  

oh.... true. You called it `base_ptr` though :) maybe `base()`?

> Username: sdkrystian  
> Created_at: 2020-05-12 01:33:20 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423412338  

Affirm


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:02:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408785819  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  46 |+     std::size_t align)
  47 |+ {
  48 |+     // must be power of 2
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:02:35 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422706603  

https://cplusplus.github.io/LWG/issue2843


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:03:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408785882  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  58 |+ monotonic_resource::
  59 |+ alloc_in_block(
  60 |+     Block& blk,
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:03:29 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422706696  

or just `b` - nothing wrong with single letter variables, we use them all the time in math.

> Username: sdkrystian  
> Created_at: 2020-05-10 23:40:34 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422717718  

Copy


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408785955  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  57 |+ void*
  58 |+ monotonic_resource::
  59 |+ alloc_in_block(
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:04:25 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422706799  

The "name" of a function includes the types of its parameters,  
so `alloc( Block& b, std::size_t n, std::size_t align )` says the same thing as `alloc_in_block( ...`


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:04:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408785988  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 110 |+   void* buffer,
 111 |+   std::size_t buffer_size,
 112 |+   std::size_t max_alloc) noexcept
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:04:56 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422706848  

The indentation is off here


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:06:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786128  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 147 |+     // cap the number of dynamically allocated bytes
 148 |+     if(allocated_ + n > max_alloc_)
 149 |+         throw std::bad_alloc();
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:06:39 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707117  

This needs to be like `detail::raise_bad_alloc` and then in that function use `BOOST_THROW_EXCEPTION`, see:  
https://github.com/CPPAlliance/json/blob/4a6a6e7cc3f02f6980e68557b9843095a578934c/include/boost/json/impl/except.ipp#L42


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:07:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786146  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 145 |+         if (auto p = alloc_in_block(initial_, n, align))
 146 |+             return p;
 147 |+     // cap the number of dynamically allocated bytes
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:07:03 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707146  

This comment adds no information


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:08:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786211  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 148 |+     if(allocated_ + n > max_alloc_)
 149 |+         throw std::bad_alloc();
 150 |+     // do this after the check for exception guarantee
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:08:18 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707264  

Tip, explain intent using the word "otherwise". Example:  
```  
// Increment after the throw,  
// otherwise no strong exception safety.  
```


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:08:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786254  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 110 |-     auto p = head_->alloc(n, align);
 155 |+     // if the requested size is greater than the block size,
 156 |+     // use the requested size instead
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:08:56 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707334  

This comment adds no information


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:10:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786370  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 156 |+     // use the requested size instead
 157 |+     const auto new_block_size = n > block_size_ - 2 * align ?
 158 |+         n + 2 * align : block_size_;
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:10:56 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707530  

Best not the use the ternary operator here, because of code coverage. It isn't quite as tidy but this is better, as it will show clearly in the coverage report whether we exercise both code paths:  
```  
std::size_t new_size;  
if( n > block_size_ - 2 * align )  
  new_size = n + 2 * align;  
else  
  new_size = block_size_;  
```  
  
Also are we checking that `n + 2 * align` does not overflow?

> Username: sdkrystian  
> Created_at: 2020-05-10 23:35:23 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422717107  

Would an IILE work?

> Username: vinniefalco  
> Created_at: 2020-05-12 01:38:23 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423413640  

ugh too try-hard


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:11:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786420  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 158 |+         n + 2 * align : block_size_;
 159 |+     alloc_block(new_block_size);
 160 |+     // apply the growth factor for the next block
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:11:37 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707608  

LOL useless comment...


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:11:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786423  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 159 |+     alloc_block(new_block_size);
 160 |+     // apply the growth factor for the next block
 161 |+     block_size_ = new_block_size * 2;
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:11:43 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707617  

This can overflow


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:12:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786491  

📁 include/boost/json/monotonic_resource.hpp

```diff
  29 |+         std::uintptr_t top;
  30 |+ 
  31 |+         initial_block() { }
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:12:34 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707698  

why not default this?

> Username: sdkrystian  
> Created_at: 2020-05-12 01:28:01 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423410888  

More characters for identical initialization semantics (albeit defaulted would make the struct trivial). I'll change it.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:12:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786506  

📁 include/boost/json/monotonic_resource.hpp

```diff
  41 |+ 
  42 |+         std::uintptr_t
  43 |+         base_ptr() const
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:12:48 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707716  

`data()`


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:13:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786525  

📁 include/boost/json/monotonic_resource.hpp

```diff
  52 |+     std::size_t block_size_;
  53 |+     block* head_ = nullptr;
  54 |+     // initial unmanaged block
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:13:04 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707754  

Useless comment


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:13:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786552  

📁 include/boost/json/monotonic_resource.hpp

```diff
  55 |+     initial_block initial_;
  56 |+     // maximum number of bytes that
  57 |+     // can be dynamically allocated
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:13:33 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707804  

Useless comment, and it is actually wrong, it should say "The limit on the total number of bytes that can be dynamically allocated by the implementation"


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-10 22:14:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-408786609  

📁 include/boost/json/monotonic_resource.hpp

```diff
  64 |+     void*
  65 |+     alloc_in_block(
  66 |+         Block& blk,
```

> Username: vinniefalco  
> Created_at: 2020-05-10 22:14:27 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r422707896  

Nice, recycle the function lol


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 01:34:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409640728  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  12 | #define BOOST_JSON_IMPL_MONOTONIC_RESOURCE_IPP
  13 | 
  14 |+ #include <algorithm>
```

> Username: vinniefalco  
> Created_at: 2020-05-12 01:34:13 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423412543  

std includes should come last. Rationale: Placing them last makes it more likely for a compiler error to occur if an include is missing.

> Username: vinniefalco  
> Created_at: 2020-05-12 01:39:04 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423413834  

why do we need `<algorithm>` that's an expensive header

> Username: sdkrystian  
> Created_at: 2020-05-12 01:50:26 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423416729  

`std::max`

> Username: vinniefalco  
> Created_at: 2020-05-12 02:27:40 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423425966  

its a shame to have to include that just for one piss-ant function

> Username: sdkrystian  
> Created_at: 2020-05-12 04:18:47 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423452353  

It's gone now


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 01:34:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409640799  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  21 |+ // ensures that the alignment of top is
  22 |+ // the strictest fundamental alignment requirement
  23 |+ struct alignas(alignof(std::max_align_t))
```

> Username: vinniefalco  
> Created_at: 2020-05-12 01:34:27 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423412599  

Is this C++11?

> Username: sdkrystian  
> Created_at: 2020-05-12 01:39:21 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423413896  

https://en.cppreference.com/w/cpp/language/alignas


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 01:35:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409641108  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  95 |+ std::size_t
  96 |+ monotonic_resource::
  97 |+ guess_next_size(std::size_t minimum)
```

> Username: vinniefalco  
> Created_at: 2020-05-12 01:35:29 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423412917  

it isn't a guess though, it is precise


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 01:37:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409641542  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 168 |+     // the closest power of 2
 169 |+     auto new_block_size = n > block_size_ ?
 170 |+         guess_next_size(n) : block_size_;
```

> Username: vinniefalco  
> Created_at: 2020-05-12 01:37:13 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423413326  

Please use `if` `else` statements instead of `?`...


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 01:41:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409642991  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  68 |+     auto aligned_addr =
  69 |+         align_address(blk.top, align);
  70 |+     if(aligned_addr + size > blk.base() + blk.size)
```

> Username: vinniefalco  
> Created_at: 2020-05-12 01:41:59 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423414571  

you should structure the LIKELY case to be true. This condition will be false more often than it is true, thus you should invert it:  
```  
if(aligned_addr + size <= blk.base() + blk.size)  
{  
    blk.top = aligned_addr + size;  
    return reinterpret_cast<void*>(aligned_addr);  
}  
return nullptr;  
```


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 02:28:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409656606  

📁 test/monotonic_resource.cpp

```diff
  22 | namespace json {
  23 | 
  24 | class pool_test
```

> Username: vinniefalco  
> Created_at: 2020-05-12 02:28:17 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423426083  

This should say `monotonic_resource_test`

> Username: sdkrystian  
> Created_at: 2020-05-12 02:35:33 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423427776  

Copy


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 02:29:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409656959  

📁 test/monotonic_resource.cpp

```diff
 104 |+             }
 105 |+             {   
 106 |+                 std::unique_ptr<unsigned char[]> buf(new unsigned char[2048]);
```

> Username: vinniefalco  
> Created_at: 2020-05-12 02:29:34 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423426355  

why not make this a local array, e.g.  
```  
char buf[2048];  
```  
? That's going to be the most common use-case.

> Username: sdkrystian  
> Created_at: 2020-05-12 02:35:24 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423427743  

I don't think it really matters here... I can add test for both.

> Username: vinniefalco  
> Created_at: 2020-05-12 02:37:11 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423428169  

its just that now we're instantiating `unique_ptr` needlessly


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 02:31:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409657557  

📁 include/boost/json/monotonic_resource.hpp

```diff
  73 |+ 
  74 |+     block&
  75 |+     allocate_new_block(std::size_t size);
```

> Username: vinniefalco  
> Created_at: 2020-05-12 02:31:35 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423426844  

this needs to be marked `inline` no?

> Username: sdkrystian  
> Created_at: 2020-05-12 02:37:08 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423428160  

Ah, forgot about header only. Should just be inline though since these aren't part of the interface, right?


---

## Comment 30

> Username: vinniefalco  
> Created_at: 2020-05-12 02:33:20 UTC  
> Url: https://github.com/boostorg/json/pull/81#issuecomment-627071446  

Warnings and errors: https://travis-ci.org/github/CPPAlliance/json

---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 12:05:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-409972070  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  31 |+         : size(size_), 
  32 |+           next(next_),
  33 |+           top(base())
```

> Username: vinniefalco  
> Created_at: 2020-05-12 12:05:23 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r423679614  

```  
        : size(size_)  
        , next(next_)  
        , top(base())  
```  
This makes it easier to reorder them


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 21:55:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410451388  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
  67 |-     return b;
  94 |+     // returns the closest power of two
  95 |+     // that is greater than requested
```

> Username: vinniefalco  
> Created_at: 2020-05-12 21:55:57 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424057031  

This comment goes on the function not inside the body


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 21:56:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410451815  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 107 |+ next_block_size(
 108 |+     std::size_t requested,
 109 |+     std::false_type)
```

> Username: vinniefalco  
> Created_at: 2020-05-12 21:56:46 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424057355  

What the hell is this true_type false_type? I have no idea. Couldn't we just name the functions differently?


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 21:57:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410452396  

📁 include/boost/json/monotonic_resource.hpp

```diff
  22 |+ #  define BOOST_JSON_MAX_ALIGN alignof(std::max_align_t)
  23 |+ # else
  24 |+ #  define BOOST_JSON_MAX_ALIGN boost::move_detail::alignment_of<boost::move_detail::max_align_t>::value
```

> Username: vinniefalco  
> Created_at: 2020-05-12 21:57:48 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424057839  

This adds a dependency on Boost.Move? Why is it accessing a detail namespace? That's bad form.


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 21:59:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410453169  

📁 include/boost/json/monotonic_resource.hpp

```diff
  60 |+     static const std::size_t min_block_size_ = 1024;
  61 |+     static const std::size_t max_block_size_ = 
  62 |+         std::numeric_limits<std::size_t>::max();
```

> Username: vinniefalco  
> Created_at: 2020-05-12 21:59:11 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424058462  

You could avoid this numeric_limits include by simply writing `std::size_t(-1)`


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-12 22:00:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410453737  

📁 include/boost/json/monotonic_resource.hpp

```diff
  89 |     ~monotonic_resource();
  90 | 
  91 |     BOOST_JSON_DECL
```

> Username: vinniefalco  
> Created_at: 2020-05-12 22:00:14 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424058913  

Why are we putting the `BOOST_JSON_DECL` modified on deleted functions?


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-13 23:18:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-410454791  

📁 include/boost/json/detail/monotonic_resource.hpp

```diff
  68 |+     return alignof(
  69 |+ #ifdef BOOST_JSON_STANDALONE
  70 |+         std::
```

> Username: vinniefalco  
> Created_at: 2020-05-13 23:18:31 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424784470  

jesus H this is ugly...never split an identifier's scope with a macro! Use  
```  
#ifdef BOOST_JSON_STANDALONE  
        std::max_align_t  
#else  
       max_align_t  
#endif  
```

> Username: sdkrystian  
> Created_at: 2020-05-13 23:33:43 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424789245  

Hah, was being clever. I'll change it.


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-13 23:19:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-411363933  

📁 include/boost/json/detail/monotonic_resource.hpp

```diff
  18 |+ namespace json {
  19 |+ namespace detail {
  20 |+ namespace dummy {
```

> Username: vinniefalco  
> Created_at: 2020-05-13 23:19:29 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424784761  

I don't like the word "dummy" appearing in my source code. Use `other` or something sterile.

> Username: sdkrystian  
> Created_at: 2020-05-13 23:33:52 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424789292  

How about `empty_types`

> Username: vinniefalco  
> Created_at: 2020-05-13 23:34:37 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424789519  

what's wrong with `other` ?

> Username: sdkrystian  
> Created_at: 2020-05-13 23:45:28 UTC  
> Updated_at: 2020-05-13 23:45:29 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424792892  

It provides no information as to what it contains


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-13 23:20:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-411364429  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 111 |+ std::size_t
 112 |+ monotonic_resource::
 113 |+ grow_block_size(std::size_t size)
```

> Username: vinniefalco  
> Created_at: 2020-05-13 23:20:46 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424785248  

This is 112% better than overloads with `true_type` and `false_type` !!

> Username: sdkrystian  
> Created_at: 2020-05-13 23:27:41 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424787416  

Epic


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-13 23:21:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-411364644  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 110 |-     auto p = head_->alloc(n, align);
 162 |+     // neither of the above was sucessful,
 163 |+     // so we need to allocate a new block.
```

> Username: vinniefalco  
> Created_at: 2020-05-13 23:21:21 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424785426  

This comment adds no information

> Username: sdkrystian  
> Created_at: 2020-05-13 23:27:35 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424787391  

Those comments just walk through the process -- makes it much faster to understand what's going on


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-13 23:21:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/81#pullrequestreview-411364745  

📁 include/boost/json/impl/monotonic_resource.ipp

```diff
 164 |+     // if the requested size is greater than the block size,
 165 |+     // use the requested size plus block overhead rounded up to
 166 |+     // the closest power of 2
```

> Username: vinniefalco  
> Created_at: 2020-05-13 23:21:38 UTC  
> Updated_at: 2020-05-13 23:44:26 UTC  
> Url: https://github.com/boostorg/json/pull/81#discussion_r424785489  

This comment also adds no information


---
