# #2148 Any buffers [Closed]

> Username: madmongo1  
> Created at: 2021-01-12 20:30:03 UTC  
> Updated at: 2022-06-21 23:39:03 UTC  
> Closed at: 2022-06-21 23:39:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2148  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-12 20:38:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-566677395  

📁 CMakeLists.txt

```diff
 149 |-     find_package(Boost COMPONENTS coroutine filesystem system REQUIRED)
 150 |-     link_libraries(Boost::coroutine Boost::filesystem Boost::system)
 147 |+     option(BEAST_ALTERNATE_PATH "" ON)
```

> Username: vinniefalco  
> Created_at: 2021-01-12 20:38:27 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556074692  

What is this funny business?

> Username: madmongo1  
> Created_at: 2021-01-12 20:41:03 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556076046  

Helps my workflow without damaging yours


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-01-12 20:39:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-758948152  

Missing linefeed at the end of the file, and some formatting that I would change but it is not critical.  
  
However, I don't see the part where it calculates space for the array of `value_type` ?

---

## Comment 3

> Username: madmongo1  
> Created_at: 2021-01-12 20:42:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-758954354  

Yes you’re right. Begin and end aren't enough

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-12 22:52:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-566768052  

📁 include/boost/beast/core/detail/any_buffers.hpp

```diff
  99 |+ 
 100 |+     any_buffers&
 101 |+     operator=(any_buffers && r);
```

> Username: vinniefalco  
> Created_at: 2021-01-12 22:52:25 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556149596  

I don't think assignment should be supported

> Username: madmongo1  
> Created_at: 2021-01-12 22:54:01 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556150287  

now you mention it, that probably makes sense


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-12 22:57:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-566770864  

📁 include/boost/beast/core/detail/impl/any_buffers.hpp

```diff
  40 |+     using atraits = std::allocator_traits<allocator_type>;
  41 |+ 
  42 |+     auto p = atraits::allocate(a, sizeof(impl_type));
```

> Username: vinniefalco  
> Created_at: 2021-01-12 22:57:50 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556152190  

`sizeof(impl_type)` needs to be something like `sizeof(impl_type) + sizeof(value_type) * std::distance(net::buffer_sequence_begin(b), net::buffer_sequence_end(b))`

> Username: madmongo1  
> Created_at: 2021-01-12 22:59:09 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556152777  

yes, that's going in next.  
Since this object never moves or copies I can actually make it variable size at construction by having the last member as a 1-array of value_type.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 10:13:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567072352  

📁 include/boost/beast/core/detail/impl/any_buffers.hpp

```diff
  45 |+             std::size_t(1)) - 1;
  46 |+ 
  47 |+     auto p = atraits::allocate(a, sizeof(impl_type) + sizeof(value_type) * extra);
```

> Username: vinniefalco  
> Created_at: 2021-01-13 10:13:01 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556407083  

This formula calculates bytes, but the allocator type is measured in units of `sizeof(impl_type)` which means you are over-allocating by quite a lot.

> Username: madmongo1  
> Created_at: 2021-01-13 13:38:45 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556524876  

oh, that's annoying.

> Username: madmongo1  
> Created_at: 2021-01-13 13:41:41 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556526703  

```  
    std::size_t extra = (std::min)(  
        static_cast<std::size_t>(  
            std::distance(net::buffer_sequence_begin(b),  
                          net::buffer_sequence_end(b))),  
        std::size_t(1)) - 1;  
      
    const auto bytes = sizeof(impl_type) + sizeof(value_type) * extra;  
    const auto units = (bytes + sizeof(impl_type) - 1) / sizeof(impl_type);  
    auto p = atraits::allocate(a, units);  
```


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 10:15:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567074465  

📁 include/boost/beast/core/detail/any_buffers.hpp

```diff
  98 |+             sizeof(value_type),
  99 |+             alignof(value_type)>::type
 100 |+                 buffer_store_[1];
```

> Username: vinniefalco  
> Created_at: 2021-01-13 10:15:34 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556408782  

There's no need for this, you have to use `this+1` to get the storage past the end of the struct, and then work off that.

> Username: madmongo1  
> Created_at: 2021-01-13 13:39:17 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556525153  

wouldn't that potentially have alignment issues?

> Username: vinniefalco  
> Created_at: 2021-01-13 14:30:38 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556560768  

you align the struct itself to the `value_type`. Boost.JSON has an example of it:  
https://github.com/boostorg/json/blob/develop/include/boost/json/impl/array.hpp#L23


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 14:31:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567277275  

📁 include/boost/beast/core/detail/any_buffers.hpp

```diff
  69 |+             value_type* dest = reinterpret_cast<value_type*>(&buffer_store_);
  70 |+             this->buffers = dest;
  71 |+             for(auto& buf : b_)
```

> Username: vinniefalco  
> Created_at: 2021-01-13 14:31:49 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556561596  

This doesn't work, you have to use `net::buffer_sequence_begin` and end.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 14:32:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567277628  

📁 include/boost/beast/core/detail/any_buffers.hpp

```diff
 107 |+         {
 108 |+             value_type* p = this->buffers + this->size;
 109 |+             while (this->size)
```

> Username: vinniefalco  
> Created_at: 2021-01-13 14:32:09 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556561839  

infinite loop here


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 14:34:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567280325  

📁 include/boost/beast/core/detail/impl/any_buffers.hpp

```diff
  49 |+     const auto bytes = sizeof(impl_type) + sizeof(value_type) * extra;
  50 |+     const auto units = (bytes + sizeof(impl_type) - 1) / sizeof(impl_type);
  51 |+     auto p = atraits::allocate(a, units);
```

> Username: vinniefalco  
> Created_at: 2021-01-13 14:34:54 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556563871  

Still wasting memory here, you want to use an allocator with the smallest type that has the alignment common to both `impl_type` and `value_type`.

> Username: madmongo1  
> Created_at: 2021-01-13 15:08:59 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556590587  

struggling to see how this would work as if we used a different allocator for the allocation and the construction, the address of the object would strictly need to be stored, as it may not be at the address returned by the allocation.

> Username: madmongo1  
> Created_at: 2021-01-13 15:14:23 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556594541  

ok, I think I have a solution

> Username: vinniefalco  
> Created_at: 2021-01-13 15:29:49 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556606919  

@glenfe ?

> Username: glenfe  
> Created_at: 2021-01-13 16:31:47 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556661755  

Given one `T` and `n` lots of  `U`, you want to allocate storage for `m` lots of `max(alignof(T), alignof(U))`. It should be obvious how to compute `m`.   
  
The first `sizeof(T)` bytes are for the one `T`, and the offset of the first `U` can always be computed at compile time.

> Username: glenfe  
> Created_at: 2021-01-13 16:37:33 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556666146  

For an example, see `<boost/smart_ptr/allocate_shared_array.hpp>` which allocates 1 control block of type `T` followed by `n` lots of a user type `U`   (which makes `allocate_shared<U[]>(n)` possible).


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-13 14:35:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-567281000  

📁 include/boost/beast/core/detail/impl/any_buffers.ipp

```diff
  37 |+ template<bool IsConst>
  38 |+ any_buffers<IsConst>::any_buffers(any_buffers const& r)
  39 |+ : impl_(copy(r.impl_))
```

> Username: vinniefalco  
> Created_at: 2021-01-13 14:35:35 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r556564377  

if `copy` is only called from here, I would just inline the function into the constructor


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:22:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-575889759  

📁 include/boost/beast/core/detail/any_buffers.hpp

```diff
  25 | namespace detail {
  26 | 
  27 |+ template<class ValueType>
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:22:37 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r564110359  

I would just use `bool IsConst` instead of `ValueType`, because `ValueType` is unconstrained while `bool` accurately reflects the actual cardinality of the value.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 23:23:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2148#pullrequestreview-575890062  

📁 include/boost/beast/core/detail/any_dynamic_buffer_v0_ref.hpp

```diff
  21 |+ 
  22 |+ /// @brief Wraps a reference to any
  23 |+ class any_dynamic_buffer_v0_ref
```

> Username: vinniefalco  
> Created_at: 2021-01-25 23:23:17 UTC  
> Updated_at: 2021-01-26 22:58:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#discussion_r564110594  

How did this slip in? We want `any_buffers` as its own commit


---

## Comment 14

> Username: stale[bot]  
> Created_at: 2021-05-29 16:10:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-850857791  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2021-09-23 15:52:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-925941362  

Didn't we measure this, and it turned out to be a loss size-wise?

---

## Comment 16

> Username: madmongo1  
> Created_at: 2021-09-23 17:24:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-926010665  

I think we felt that the gains did not justify the effort.  
  
  
On Thu, 23 Sept 2021 at 17:53, Vinnie Falco ***@***.***>  
wrote:  
  
> Didn't we measure this, and it turned out to be a loss size-wise?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2148#issuecomment-925941362>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSNTDRN2EY4HWPXD2QTUDNEN5ANCNFSM4V7YIV4A>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 17

> Username: stale[bot]  
> Created_at: 2022-01-09 01:16:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-1008206040  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2022-06-21 23:39:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2148#issuecomment-1162464373  

This was a mess

---
