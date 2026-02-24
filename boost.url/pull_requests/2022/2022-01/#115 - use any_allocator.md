# #115 use any_allocator [Closed]

> Username: alandefreitas  
> Created at: 2022-01-20 00:29:46 UTC  
> Updated at: 2022-06-08 13:34:38 UTC  
> Closed at: 2022-01-26 23:44:21 UTC  
> Url: https://github.com/boostorg/url/pull/115  

Use `any_allocator` in all classes that previously used `const_string::allocator`

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:34:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857549091  

📁 include/boost/url/impl/const_string.hpp

```diff
  71 |     allocator_type al(n, a);
  70 |-     auto p = ::new(
  72 |+     impl* p = ::new(
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:34:33 UTC  
> Updated_at: 2022-01-20 01:34:34 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788281014  

why


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:37:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857550705  

📁 include/boost/url/impl/params.hpp

```diff
  31 |         std::size_t i,
  32 |-         const_string::allocator a) noexcept
  32 |+         any_allocator<char> a) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:37:48 UTC  
> Updated_at: 2022-01-20 01:37:49 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788282244  

`const&`?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857550800  

📁 include/boost/url/impl/params_view.hpp

```diff
  35 |         string_view s,
  36 |-         const_string::allocator a) noexcept;
  36 |+         any_allocator<char> a) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:00 UTC  
> Updated_at: 2022-01-20 01:38:01 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788282306  

`const&`?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857550863  

📁 include/boost/url/impl/params_view.hpp

```diff
  41 |         int,
  42 |-         const_string::allocator a) noexcept;
  42 |+         any_allocator<char> a) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:07 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788282347  

`const&`?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857551021  

📁 include/boost/url/impl/params_view.hpp

```diff
  41 |         int,
  42 |-         const_string::allocator a) noexcept;
  42 |+         any_allocator<char> a) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:30 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788282482  

the `int` should be last, it is a dummy argument


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857551090  

📁 include/boost/url/impl/params_view.ipp

```diff
  46 |     std::size_t const nv,
  47 |-     const_string::allocator a)
  47 |+     any_allocator<char> a)
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:38:39 UTC  
> Updated_at: 2022-01-20 01:38:40 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788282547  

`const&`?


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:56:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857559427  

📁 include/boost/url/impl/const_string.hpp

```diff
  59 |-             auto a(a_);
  60 |+             auto a(this->get());
  61 |+             this->impl::~impl();
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:56:45 UTC  
> Updated_at: 2022-01-20 01:56:46 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788289211  

doesn't `this->~impl()` compile?


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 01:58:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-857560352  

📁 include/boost/url/url_view.hpp

```diff
 139 |     // return offset of id
 140 |-     BOOST_URL_CONSTEXPR
 140 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-01-20 01:58:43 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r788289925  

if you are removing the constexpr, do we need `inline`?


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 18:33:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858657317  

📁 include/boost/url/impl/const_string.hpp

```diff
 107 |+     increment_refs();
 108 |+     static_cast<string_view&>(*this) =
 109 |+         static_cast<const string_view&>(other);
```

> Username: vinniefalco  
> Created_at: 2022-01-20 18:33:37 UTC  
> Updated_at: 2022-01-20 18:33:38 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789048162  

the second static_cast is probably not needed

> Username: alandefreitas  
> Created_at: 2022-01-20 21:45:46 UTC  
> Updated_at: 2022-01-20 21:45:47 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789174409  

Yes. It does work without the static_cast here, but it emits a warning about slicing.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:04:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858746894  

📁 include/boost/url/const_string.hpp

```diff
  14 | #include <boost/url/string_view.hpp>
  15 |+ #include <boost/url/any_allocator.hpp>
  16 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:04:30 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789111179  

alphabetical


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:05:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858748107  

📁 include/boost/url/impl/const_string.hpp

```diff
  51 |+     if(n == 0)
  52 |+     {
  53 |+         static_cast<string_view&>(*this) = { nullptr, n };
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:05:47 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789112004  

or  
```  
*this = string_view();  
```  
?

> Username: alandefreitas  
> Created_at: 2022-01-20 21:49:21 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789176528  

```  
error: no viable overloaded '='  
```  
  
It might also imply we want to call the string_view constructor, which would lose the allocator.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858749355  

📁 include/boost/url/const_string.hpp

```diff
  49 |-     base* p_ = nullptr;
  52 |+     ref_counter_t* refs_ = nullptr;
  53 |+     any_allocator<ref_counter_t> alloc_;
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:06 UTC  
> Updated_at: 2022-01-20 20:07:07 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789112848  

are we relying on `any_allocator` being _DefaultConstructible_ ?


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858749580  

📁 include/boost/url/const_string.hpp

```diff
  55 |-         Allocator const& a,
  56 |-         char*& dest);
  55 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:20 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789112992  

I thought we were gonna put `inline` on the definitions?

> Username: alandefreitas  
> Created_at: 2022-01-20 21:50:57 UTC  
> Updated_at: 2022-01-20 21:50:58 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789177477  

I was. Then you forbid me and asked me to open an issue (https://github.com/CPPAlliance/url/issues/109).

> Username: vinniefalco  
> Created_at: 2022-01-20 23:30:17 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789229502  

no I said don't change everything that's already there just open an issue for it (which you did), but we should use it for any new code or code that is modified as a result of other changes

> Username: alandefreitas  
> Created_at: 2022-01-21 00:25:53 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789250637  

My understanding was this shouldn't be done unless we do it for boost.url, boost.json, and boost.beast for some reason. Anyway, I'm not going to look for the message. I'm just going to put `inline` in the implementation from now on then.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858749943  

📁 include/boost/url/impl/const_string.hpp

```diff
  12 | 
  13 |- #include <boost/url/detail/over_allocator.hpp>
  13 | #include <atomic>
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:41 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789113227  

wasn't this already included from const_string.hpp?


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858750081  

📁 include/boost/url/impl/const_string.hpp

```diff
  20 |+ decrement_refs() {
  21 |+     if( refs_ &&
  22 |+         refs_->fetch_sub(1, std::memory_order_release) == 1) {
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:07:49 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789113314  

brace


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:10:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858752247  

📁 include/boost/url/impl/const_string.hpp

```diff
  59 |+     auto constexpr S = sizeof(U);
  60 |+     using A = allocator_rebind_t<any_allocator<ref_counter_t>, U>;
  61 |+     A aa{alloc_};
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:10:09 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789114873  

why not just make `alloc_` the correct type?


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:10:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858752688  

📁 include/boost/url/impl/const_string.hpp

```diff
  62 |+     refs_ = reinterpret_cast<ref_counter_t*>(
  63 |+         allocator_allocate(
  64 |+         aa, (sizeof(ref_counter_t) + n + S - 1) / S)
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:10:39 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789115206  

do we care if this integer calculation overflows?

> Username: vinniefalco  
> Created_at: 2022-01-20 20:10:49 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789115330  

@pdimov ?


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 20:11:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858753245  

📁 include/boost/url/impl/const_string.hpp

```diff
  66 |+     allocator_construct(alloc_, refs_, 1);
  67 |+     dest = reinterpret_cast<char*>(refs_ + 1);
  68 |+     static_cast<string_view&>(*this) = {dest, n};
```

> Username: vinniefalco  
> Created_at: 2022-01-20 20:11:14 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789115597  

wouldn't this work?  
```  
*this = string_view( dest, n );  
```  
?

> Username: alandefreitas  
> Created_at: 2022-01-20 21:57:14 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789181288  

```  
error: No viable overloaded '='  
```  
  
If it went to the constructor, it would be (i) trying to construct again with std::allocator and (i) an infinite recursion.   
If it went to `operator=`, then it would be wrong because we can´t assign string_view to a constant string.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 23:29:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858909738  

📁 include/boost/url/authority_view.hpp

```diff
 116 |     BOOST_URL_CONSTEXPR
 117 |-     auto
 117 |+     auto
```

> Username: vinniefalco  
> Created_at: 2022-01-20 23:29:16 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789229057  

why are we adding a space?


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 23:37:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858914106  

📁 include/boost/url/const_string.hpp

```diff
  51 | {
  47 |-     struct base;
  52 |+     using ref_counter_t = std::atomic<std::uint8_t>;
```

> Username: vinniefalco  
> Created_at: 2022-01-20 23:37:10 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789232271  

at least uint32_t


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-20 23:39:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/115#pullrequestreview-858915025  

📁 include/boost/url/impl/const_string.hpp

```diff
  61 |+     if (!increment_refs())
  62 |+     {
  63 |+         *this = const_string(
```

> Username: vinniefalco  
> Created_at: 2022-01-20 23:39:01 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789232938  

we are calling a function that can throw from a constructor marked noexcept. We shouldn't be trying to "handle" the case where the counter overflows. Just use a 32-bit unsigned counter.

> Username: alandefreitas  
> Created_at: 2022-01-21 00:44:26 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789257265  

> We shouldn't be trying to "handle" the case where the counter overflows  
  
Why not?   
  
- This saves more than 7 bytes per string in the pool: the size bytes and alignment bytes. Not to mention what we have already saved from `impl`.  
- Even most strings are usually going to be shorter than what we just saved here.   
- If we promise the library avoids allocations, pool memory is also much more valuable than heap memory. Users could be disappointed if they need a pool larger than the strings themselves or even a pool that takes more than it needs to.  
- There's no practical use case for more than 255 copy-like references of the string. If the user gets to this point, they are either doing something wrong or don't mind an extra string every 255 copies.  
- uint8_t allows the reference counter and the string to be aligned, which simplifies the allocation logic immensely: a single allocator, no rebinding, no over_allocator, not anything.  
- It can't be smaller than uint8_t, which makes it less arbitrary and much easier to justify.  
- `uint8_t` is the only size for which there is a qualitative difference in the pros and cons. For any other size without handling overflow (16, 32, 64), we lose all the benefits, the cons all apply, and any difference is only quantitative, making any limit (2^16, 2^32, 2^64) as justifiable as the other.

> Username: pdimov  
> Created_at: 2022-01-21 00:51:32 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789259769  

You need to remove the noexcept from the constructor if you do this, because throwing when noexcept calls `std::terminate`.

> Username: alandefreitas  
> Created_at: 2022-01-21 00:54:16 UTC  
> Updated_at: 2022-01-21 00:54:17 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789260803  

Oh... sure. No problem.

> Username: pdimov  
> Created_at: 2022-01-21 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789263293  

I'm not sure this is correct. Suppose you get to the part in `increment_refs` where the old value is 255, and then the thread is suspended. The counter is now 0. Suppose now that three threads make copies. The counter is now 3. The thread is resumed, does fetch_sub, gets the value to 2. Doesn't seem good.  
  
Using compare_exchange(_strong) would make it correct but probably less efficient. I'm not sure whether it's possible to make it correct using only ++/--.

> Username: alandefreitas  
> Created_at: 2022-01-21 15:23:27 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r789749839  

You're right. The old rationale was that only one thread was going to return 0 anyway so this could be relaxed. I didn't consider the effects of the final counter being wrong.     
  
So I realized the best pattern here is probably the same we use for decrementing the counter, with `fetch_sub` instead of `deallocate`. Not much better than acq_rel in practice, but it seems like the correct implementation.

> Username: vinniefalco  
> Created_at: 2022-01-21 23:18:43 UTC  
> Updated_at: 2022-01-21 23:18:46 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r790054226  

use a 32-bit counter and dont get fancy with copying

> Username: pdimov  
> Created_at: 2022-01-22 00:35:01 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r790075020  

This is not a memory order problem; it happens with seq_cst as well.

> Username: vinniefalco  
> Created_at: 2022-01-22 02:48:49 UTC  
> Url: https://github.com/boostorg/url/pull/115#discussion_r790093976  

we are prematurely optimizing again, 255 is too small for the maximum refs, and it causes us to have to include ridiculous code to copy the string which is not justifiable without overwhelming field experience, of which we have none.


---

## Comment 22

> Username: codecov[bot]  
> Created_at: 2022-01-22 01:35:57 UTC  
> Updated_at: 2022-01-22 07:30:53 UTC  
> Url: https://github.com/boostorg/url/pull/115#issuecomment-1019008427  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#115](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (19c39f9) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/3079b7f56b6d3f3e3a65606b55745d3116f5a3a5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3079b7f) will **increase** coverage by `0.07%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/115/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #115      +/-   ##  
===========================================  
+ Coverage    96.09%   96.17%   +0.07%       
===========================================  
  Files          107      107                
  Lines         5485     5441      -44       
===========================================  
- Hits          5271     5233      -38       
+ Misses         214      208       -6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `93.71% <ø> (+0.54%)` | :arrow_up: |  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `99.37% <ø> (ø)` | |  
| [include/boost/url/impl/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [8 more](https://codecov.io/gh/CPPAlliance/url/pull/115/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [3079b7f...19c39f9](https://codecov.io/gh/CPPAlliance/url/pull/115?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
