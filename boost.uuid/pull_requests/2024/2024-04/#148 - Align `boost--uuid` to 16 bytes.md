# #148 Align `boost::uuid` to 16 bytes [Closed]

> Username: Lastique  
> Created at: 2024-04-23 14:41:21 UTC  
> Updated at: 2024-07-04 15:45:01 UTC  
> Closed at: 2024-07-04 14:24:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/148  

This improves performance of operations on UUIDs as the UUID object now does not(*) cross page boundary. It may also improve code generation by compilers, which can now assume that loads and stores of UUIDs are not as expensive and therefore it may be more preferable to use vector instructions for that.  
  
(*) It is technically possible that the `boost::uuid` object is still not properly aligned under some conditions. For example, on some 32-bit targets the stack is not aligned to 16 bytes, so placing the UUID object on the unaligned stack may result in unaligned UUID. For this reason, this commit does not change the SIMD intrinsics used in `boost::uuid` operations to their aligned equivalents. Using unaligned load/store instructions on actually aligned memory location on modern CPUs has the same performance as using aligned instructions, so the performance benefit is still there.  
  
Closes https://github.com/boostorg/uuid/issues/139.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-04-23 19:52:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#issuecomment-2073318113  

I've updated the PR to only require alignment of 4 on 32-bit platforms. At least, on Windows, 32-bit processes only get 4-byte aligned stack.

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2024-05-14 11:36:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/148#pullrequestreview-2055089979  

📁 include/boost/uuid/uuid.hpp

```diff
 142 |+     // On most 32-bit platforms, default stack and dynamic memory alignment is lower than 16
 143 |+     // (typically 4 or 8), which makes it problematic to require uuid alignment of 16.
 144 |+ #if (defined(__SIZEOF_POINTER__) && (__SIZEOF_POINTER__ >= 8)) || \
```

> Username: jeking3  
> Created_at: 2024-05-14 11:35:20 UTC  
> Updated_at: 2024-05-14 11:36:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#discussion_r1599866126  

How will this interact if the class is placed into another POD using packed alignment?

> Username: Lastique  
> Created_at: 2024-05-14 18:19:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#discussion_r1600466807  

Packed structs are not portable, and the behavior varies from one compiler to another. See [here](https://godbolt.org/z/49aEz1EGo). Basically, gcc and clang on System V ABI override `uuid` alignment, and MSVC respects `uuid` alignment and packs other data members with no explicit alignment requirements.


---

## Comment 3

> Username: Lastique  
> Created_at: 2024-05-14 19:21:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#issuecomment-2110983313  

@pdimov What's the purpose of `test_alignment`? Is it ok to just test that `offsetof % alignof(uuid) == 0`?

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-05-14 19:24:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#issuecomment-2110987204  

It's something that works today.

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-05-14 19:31:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#issuecomment-2110997044  

Do you mean that `alignof(uuid)` is guaranteed (i.e. documented) to be 1? I don't remember seeing this in the docs.  
  
In any case, my question was what was the intended tested feature in this test. IOW, are you testing that `alignof(uuid)` is exactly 1 or are you testing that the compiler is able to align `uuid` in a struct by adding padding? Depending on what is it you're testing, the test could be updated differently, or the PR could be rejected altogether.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-07-04 14:24:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/148#issuecomment-2209121169  

I don't think libraries should overalign their public types, sorry.  
  
I tried to align `uuid` like `__uint128_t`, but this type is also overaligned on some platforms, so I left it at `std::uint64_t`.  
  
Maybe one day we'll get `std::uint128_t` which will be a standard integer type and hence not overaligned, but until that happens, uint64_t it is.

---
