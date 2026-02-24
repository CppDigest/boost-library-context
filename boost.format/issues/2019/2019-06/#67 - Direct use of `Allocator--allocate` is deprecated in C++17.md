# #67 - Direct use of `Allocator::allocate` is deprecated in C++17 [Closed]

> Username: uecasm  
> Created at: 2019-06-17 04:44:28 UTC  
> Updated at: 2020-05-26 02:37:23 UTC  
> Closed at: 2020-05-26 02:37:22 UTC  
> Url: https://github.com/boostorg/format/issues/67  

https://github.com/boostorg/format/blob/7f4131ba2dad6c94388268a0554382858a94f794/include/boost/format/alt_sstream_impl.hpp#L40-L45  
  
Should use `allocator_traits` when at least C++11 is detected instead; the above code produces a deprecation warning in some compiler configurations.  
  
There might be some other code that should be updated as well, but this was the only one that I noticed a warning being flagged on (when compiling with VS2017 in C++17 mode).

---

## Comment 1

> Username: bansan85  
> Created at: 2019-10-28 11:49:52 UTC  
> Url: https://github.com/boostorg/format/issues/67#issuecomment-546911466  

Right. LibreOffice uses the following [patch](https://cgit.freedesktop.org/libreoffice/core/commit/?id=677c8de4fa79cd9b278b142013ba7f1c9e4e41c3) :  
  
```  
-newptr = alloc_.allocate(new_size, is_allocated_? oldptr : 0);  
+newptr = std::allocator_traits<compat_allocator_type>::allocate(alloc_, new_size, is_allocated_? oldptr : 0);  
```

---

## Comment 2

> Username: jwakely  
> Created at: 2020-03-30 12:00:51 UTC  
> Updated at: 2020-03-30 12:01:16 UTC  
> Url: https://github.com/boostorg/format/issues/67#issuecomment-605957634  

The issue title is misleading: only the two argument form of `std::allocator<T>::allocate` is deprecated (i.e. the form taking a 'hint' argument). And that form has not been required for user-defined allocators since C++11: allocators are only required to provide `allocate(size_type n)`, so portable use of a hint has required going via `allocator_traits<Alloc>::allocate` for almost a decade.  
  
Using `allocator_traits` directly is necessary to portably pass a hint, but it would also be possible to just stop passing a hint argument. Do any real allocators make use of the hint?

---

## Comment 3

> Username: uecasm  
> Created at: 2020-03-30 20:21:09 UTC  
> Updated at: 2020-03-30 20:27:38 UTC  
> Url: https://github.com/boostorg/format/issues/67#issuecomment-606226280  

While that's true, as noted in the original description at least MSVC's STL does generate a deprecation warning on direct calls to `std::allocator::allocate`, which is how I noticed this in the first place.  Or it's that `std::allocator<void>` itself is entirely deprecated.    
  
Related, there are also no calls to `construct/destroy`, which *do* have to go through `allocator_traits`.  They're guaranteed no-ops for `std::allocator` and all standard char types, but might possibly be interesting for a non-standard allocator.  Probably not a big deal though.  
  
It also doesn't follow the allocator propagation rules and doesn't have a allocator instance constructor, which might be a bigger deal.  As such it doesn't qualify as an [`AllocatorAwareContainer`](https://en.cppreference.com/w/cpp/named_req/AllocatorAwareContainer) -- which it wouldn't anyway since it's not a `Container`, but it's _almost_ one.

---

## Comment 4

> Username: jwakely  
> Created at: 2020-03-30 21:34:42 UTC  
> Url: https://github.com/boostorg/format/issues/67#issuecomment-606263882  

Yeah, this code looks like it takes an allocator argument, but only actually works when that argument is `std::allocator`, so it might as well not bother pretending to support allocators at all. Oh well.

---

## Comment 5

> Username: glenfe  
> Created at: 2020-05-26 02:37:22 UTC  
> Url: https://github.com/boostorg/format/issues/67#issuecomment-633777466  

Resolved in 5884c3d8140cdcd2d1bef374fcc216244d5faaa5.
