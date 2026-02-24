# #334 Fix issue with constructing an FOA container from a class with a template conversion operator [Open]

> Username: k3DW  
> Created at: 2026-01-01 00:26:01 UTC  
> Updated at: 2026-02-13 17:30:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/334  

Closes #331

---

## Comment 1

> Username: joaquintides  
> Created_at: 2026-01-01 17:09:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3703909559  

Not entirely sure, but wouldn't a workaround based on `boost::unordered::detail::type_identity` achieve the same result with less hassle?

---

## Comment 2

> Username: k3DW  
> Created_at: 2026-01-01 19:05:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3704008218  

> Not entirely sure, but wouldn't a workaround based on `boost::unordered::detail::type_identity` achieve the same result with less hassle?  
  
Do you mean something like this?  
  
```cpp  
template <bool avoid_explicit_instantiation = true>  
unordered_node_map(typename detail::type_identity<  
  concurrent_node_map<Key, T, Hash, KeyEqual, Allocator> >::type&& other)  
    : table_(std::move(other.table_))  
{  
}  
```  
  
This doesn't work because the constructor argument isn't dependent on any template argument. But the following also doesn't work.  
  
```cpp  
template <class TT, bool B> struct type_identity_with_bool  
{  
  using type = TT;  
};  
template <bool avoid_explicit_instantiation = true>  
unordered_node_map(typename type_identity_with_bool<  
  concurrent_node_map<Key, T, Hash, KeyEqual, Allocator>,  
  avoid_explicit_instantiation>::type&& other)  
    : table_(std::move(other.table_))  
{  
}  
```  
  
I'm not exactly sure why _this_ one doesn't work, but I think it's because of the defaulted bool template argument. I'd appreciate some more suggestions though, if this isn't what you meant. I'm all out of ideas.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2026-01-01 20:51:43 UTC  
> Updated_at: 2026-01-01 20:57:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3704096430  

> Do you mean something like this?  
>  
> template <bool avoid_explicit_instantiation = true>  
> unordered_node_map(typename detail::type_identity<  
>  concurrent_node_map<Key, T, Hash, KeyEqual, Allocator> >::type&& other)  
>     : table_(std::move(other.table_))  
> {  
> }  
>   
> This doesn't work because the constructor argument isn't dependent on any template argument.   
  
~~Umm, seems to work for me:~~  
  
~~https://godbolt.org/z/hjP83o3eE~~  
  
Correction: it doesn't work for me either.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2026-01-01 21:22:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3704121330  

Maybe this?  
```cpp  
      template <  
        typename T2,  
        typename std::enable_if<std::is_same<T, T2>::value, int>::type = 0  
      >  
      unordered_node_map(  
        concurrent_node_map<Key, T2, Hash, KeyEqual, Allocator>&& other)  
          : table_(std::move(other.table_))  
      {  
      }  
```

---

## Comment 5

> Username: k3DW  
> Created_at: 2026-01-01 21:39:00 UTC  
> Updated_at: 2026-01-01 21:39:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3704134574  

> Maybe this?  
  
That will work for the main failing case, but it will change the overload resolution so that you can no longer construct an `unordered_node_map` from anything that converts to `concurrent_node_map`. I have no idea whether this would or wouldn't break someone's build, but I think it's better to try and find a solution that keeps the same behaviour as before.  
  
That said, the solution that I currently have up in this PR also doesn't work on Windows, so I'm not done exploring how to fix this.

---

## Comment 6

> Username: k3DW  
> Created_at: 2026-01-01 22:30:06 UTC  
> Updated_at: 2026-01-02 04:44:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3704189844  

At this point, we have multiple options for how to write the 1st constructor, where we want to match _exactly_ an rvalue reference to the CFOA counterpart container.  
  
For the 2nd constructor, this is where the issues are happening. It seems that `std::is_convertible<...>::value` is causing the other container to be instantiated on certain platforms (Clang 7, GCC >= 10, MSVC >= 14.2). This is the condition that's causing the instantiation.  
  
```cpp  
std::is_convertible<U&&, concurrent_node_map<Key, T, Hash,  
                           KeyEqual, Allocator> >::value,  
```  
  
~~On MSVC 14.3, I got this condition working instead. But it doesn't work on Clang 21, the version I'm testing locally.~~  
  
```cpp  
std::is_void_v<std::void_t<decltype(std::declval<U&&>()  
    .operator concurrent_node_map<Key, T, Hash, KeyEqual,  
      Allocator>())> >  
```  
  
~~The weird thing about this one, it relies on `std::is_void_v` and cannot use `std::is_void<...>::value`, otherwise the static_assert will fail in the test. We might be able to write some macro code to recreate the behaviour of `std::is_void_v` using the platform-specific intrinsic functions, so that might be fine. I'm not sure if this idea is worth pursuing though.~~  
  
Overall I think it's fine to check for the existence of `operator concurrent_node_map()`, instead of just general "convertibility". Since we know we're dealing with class types, "convertibility" is just conversion operators and constructors, so we're removing constructors from that list. If `concurrent_node_map` has a 1 argument implicit constructor from type `U`, `unordered_node_map` has that same implicit constructor from type `U`. Therefore there's no need to check for the constructors here, if we can get away with only checking conversion operators.  
  
~~Neither of these methods work on GCC anyway, so I still don't know what to do.~~  
  
Edit from later: It turns out that my suggested MSVC solution doesn't actually work on MSVC afterall. But I _did_ find a solution that works on GCC, using only the presence of the conversion operator. This doesn't work on MSVC or Clang.  
  
```cpp  
nullptr !=  
  &std::remove_reference<U>::type::operator concurrent_node_map<Key,  
    T, Hash, KeyEqual, Allocator>  
```

---

## Comment 7

> Username: joaquintides  
> Created_at: 2026-02-13 17:30:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/334#issuecomment-3898417057  

@k3DW it doesn't look like we're going to come up with a totally transparent solution right now, and Boost 1.91 is approaching, so, given that the reported issues looks important enough, I'd go with something like https://github.com/boostorg/unordered/pull/334#issuecomment-3704121330 and maybe file an issue to potentially continue investigating after the release.

---
