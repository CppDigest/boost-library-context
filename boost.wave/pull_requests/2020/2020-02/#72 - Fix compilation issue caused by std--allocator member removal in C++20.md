# #72 Fix compilation issue caused by std::allocator member removal in C++20 [Merged]

> Username: stac47  
> Created at: 2020-02-17 09:12:49 UTC  
> Updated at: 2020-02-19 17:04:37 UTC  
> Merged at: 2020-02-19 17:04:37 UTC  
> Closed at: 2020-02-19 17:04:37 UTC  
> Url: https://github.com/boostorg/wave/pull/72  

This PR fixes the compilation problem caused by the removal of some member typdef in std::allocator since C++20 standard.  
  
```  
In file included from ./boost/wave/wave_config.hpp:230,  
                 from libs/wave/src/instantiate_cpp_exprgrammar.cpp:14:  
./boost/wave/util/flex_string.hpp: In instantiation of ‘class boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> >’:  
./boost/wave/util/flex_string.hpp:1417:7:   required from ‘class boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > >’  
./boost/wave/util/file_position.hpp:85:17:   required from ‘struct boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >’  
./boost/wave/util/file_position.hpp:161:16:   required from here  
./boost/wave/util/flex_string.hpp:1205:41: error: no type named ‘reference’ in ‘class boost::wave::util::AllocatorStringStorage<char>’  
 1205 |     typedef typename Storage::reference reference;  
      |                                         ^~~~~~~~~  
./boost/wave/util/flex_string.hpp: In instantiation of ‘class boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > >’:  
./boost/wave/util/file_position.hpp:85:17:   required from ‘struct boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >’  
./boost/wave/util/file_position.hpp:161:16:   required from here  
./boost/wave/util/flex_string.hpp:1467:35: error: no type named ‘reference’ in ‘class std::allocator<char>’  
 1467 |     typedef typename A::reference reference;  
      |                                   ^~~~~~~~~  
./boost/wave/util/flex_string.hpp:1468:41: error: no type named ‘const_reference’ in ‘class std::allocator<char>’  
 1468 |     typedef typename A::const_reference const_reference;  
      |                                         ^~~~~~~~~~~~~~~  
./boost/wave/util/flex_string.hpp:1469:33: error: no type named ‘pointer’ in ‘class std::allocator<char>’  
 1469 |     typedef typename A::pointer pointer;  
      |                                 ^~~~~~~  
./boost/wave/util/flex_string.hpp:1470:39: error: no type named ‘const_pointer’ in ‘class std::allocator<char>’  
 1470 |     typedef typename A::const_pointer const_pointer;  
      |                                       ^~~~~~~~~~~~~  
```

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-17 17:07:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/72#pullrequestreview-359877712  

📁 include/boost/wave/util/flex_string.hpp

```diff
 552 |-         return A::allocate(1 + (sz - 1) / sizeof(E),
 553 |-             static_cast<const char*>(p));
 552 |+         return A::allocate(1 + (sz - 1) / sizeof(E));
```

> Username: hkaiser  
> Created_at: 2020-02-17 17:07:01 UTC  
> Updated_at: 2020-02-19 16:16:16 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r380294568  

This change needs to be protected by a feature macro or similar to retain compatibility with older compilers. The new signature is available starting C++17 only.

> Username: hkaiser  
> Created_at: 2020-02-19 13:11:48 UTC  
> Updated_at: 2020-02-19 16:16:16 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r381280023  

Better yet, this could be replaced with the equivalent `allocator_traits` incantation, e.g.:  
```diff  
-        return A::allocate(1 + (sz - 1) / sizeof(E));  
+        return std::allocator_traits<A>::allocate(1 + (sz - 1) / sizeof(E));  
```


---

## Review 2 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-17 17:09:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/72#pullrequestreview-359879101  

📁 include/boost/wave/util/flex_string.hpp

```diff
1468 |+     typedef typename A::value_type const& const_reference;
1469 |+     typedef typename A::value_type* pointer;
1470 |+     typedef typename A::value_type const* const_pointer;
```

> Username: hkaiser  
> Created_at: 2020-02-17 17:09:49 UTC  
> Updated_at: 2020-02-19 16:16:16 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r380295624  

Wouldn't it be better to change this to using `std::allocator_traits` (if available)?

> Username: stac47  
> Created_at: 2020-02-19 14:50:30 UTC  
> Updated_at: 2020-02-19 16:16:16 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r381339891  

Thanks for your remarks. I update it.


---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-02-18 14:40:51 UTC  
> Url: https://github.com/boostorg/wave/pull/72#issuecomment-587492540  

It would be nice to have this fix for 1.73. If @stac47 doesn't do the allocator_traits change maybe we should?

---

## Review 4 [Approved]

> Username: hkaiser  
> Created_at: 2020-02-19 14:55:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/72#pullrequestreview-361179721  

LGTM, thanks a lot!

---

## Review 5 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-19 16:11:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/72#pullrequestreview-361223391  

📁 include/boost/wave/util/flex_string.hpp

```diff
 555 |+         return std::allocator_traits<A>::allocate(*this, 1 + (sz - 1) / sizeof(E),
 556 |+ #endif
 557 |             static_cast<const char*>(p));
```

> Username: hkaiser  
> Created_at: 2020-02-19 16:11:26 UTC  
> Updated_at: 2020-02-19 16:16:16 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r381377108  

Please duplicate this line inside both branches of the #if/#else.

> Username: stac47  
> Created_at: 2020-02-19 16:16:38 UTC  
> Updated_at: 2020-02-19 16:16:39 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r381381036  

Done

> Username: hkaiser  
> Created_at: 2020-02-19 16:20:06 UTC  
> Updated_at: 2020-02-19 16:20:07 UTC  
> Url: https://github.com/boostorg/wave/pull/72#discussion_r381383625  

Thanks a lot!


---

## Comment 6

> Username: hkaiser  
> Created_at: 2020-02-19 16:20:43 UTC  
> Url: https://github.com/boostorg/wave/pull/72#issuecomment-588305912  

@jefftrull I think this is good to go now. What do you think?

---

## Comment 7

> Username: jefftrull  
> Created_at: 2020-02-19 16:36:29 UTC  
> Url: https://github.com/boostorg/wave/pull/72#issuecomment-588314734  

LGTM also but let's let CI finish running just to be sure.  
I should explore getting C++20 in Travis!  
Thanks for the contribution @stac47

---
