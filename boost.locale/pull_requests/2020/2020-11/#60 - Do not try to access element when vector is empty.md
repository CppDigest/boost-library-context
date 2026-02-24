# #60 Do not try to access element when vector is empty [Closed]

> Username: OznOg  
> Created at: 2020-11-14 13:06:58 UTC  
> Updated at: 2021-12-10 20:41:33 UTC  
> Closed at: 2021-12-10 20:41:32 UTC  
> Url: https://github.com/boostorg/locale/pull/60  

Trying to access tmp[0] causes a crash on Fedora when assertion on STL  
are enabled.  
  
/usr/include/c++/10/bits/stl_vector.h:1045: std::vector<_Tp, _Alloc>::reference std::vector<_Tp, _Alloc>::operator[](std::vector<_Tp, _Alloc>::size_type) [with _Tp = unsigned char; _Alloc = std::allocator<unsigned char>; std::vector<_Tp, _Alloc>::reference = unsigned char&; std::vector<_Tp, _Alloc>::size_type = long unsigned int]: Assertion '__builtin_expect(__n < this->size(), true)' failed.  
  
This patch just passes nullptr as pointer to getSortKey() when tmp size  
is 0, preventing dereferencing elements in empty vector.  
  
I guess that &tmp[0] should be optimized as 'no real access' when  
disabling assertion, but actually leads to crash when assert are  
enabled.

---

## Review 1 [Commented]

> Username: jwakely  
> Created_at: 2020-11-20 10:39:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/60#pullrequestreview-535315020  

📁 src/icu/collator.cpp

```diff
  95 |                     icu::Collator *collate = get_collator(level);
  96 |-                     int len = collate->getSortKey(str,&tmp[0],tmp.size());
  96 |+                     int len = collate->getSortKey(str,tmp.empty()?nullptr:&tmp[0],tmp.size());
```

> Username: jwakely  
> Created_at: 2020-11-20 10:39:57 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527604154  

Using `nullptr` assumes this file is compiled as C++11 or later. I don't know if Boost.Locale supports building as C++03 (strictly speaking `uint8_t` is not available before C++11), but if using C++11 is OK, then this might as well just use `tmp.data()`. That is present since C++11.

> Username: jwakely  
> Created_at: 2020-11-20 10:43:26 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527606026  

Another option would be to just add `if (b == e) return std::vector<uint8_t>();` to the start of the function. There's no point doing all this work for an empty string.

> Username: jwakely  
> Created_at: 2020-11-20 10:47:52 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527608499  

Or, to ensure NRVO is used, move the definition of `tmp` before the definition of `str` so the named object can be returned:  
  
```  
                {  
                     std::vector<uint8_t> tmp;  
                     if (b == e)  
                         return tmp;  
                     icu::UnicodeString str=cvt_.icu(b,e);  
                     ...  
```

> Username: OznOg  
> Created_at: 2020-11-20 12:21:55 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527655769  

FYI  
I tried this, but still have the crash:  
```  
diff --git a/src/icu/collator.cpp b/src/icu/collator.cpp  
index dc59e8c..4512fac 100644  
--- a/src/icu/collator.cpp  
+++ b/src/icu/collator.cpp  
@@ -89,11 +89,13 @@ namespace boost {  
                  
                 std::vector<uint8_t> do_basic_transform(level_type level,CharType const *b,CharType const *e) const   
                 {  
-                    icu::UnicodeString str=cvt_.icu(b,e);  
                     std::vector<uint8_t> tmp;  
+                    if (b == e)  
+                        return tmp;  
+                    icu::UnicodeString str=cvt_.icu(b,e);  
                     tmp.resize(str.length());  
                     icu::Collator *collate = get_collator(level);  
                     int len = collate->getSortKey(str,&tmp[0],tmp.size());  
                     if(len > int(tmp.size())) {  
                         tmp.resize(len);  
                         collate->getSortKey(str,&tmp[0],tmp.size());  
  
```


---

## Review 2 [Commented]

> Username: jwakely  
> Created_at: 2020-11-20 10:45:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/60#pullrequestreview-535318576  

📁 src/icu/collator.cpp

```diff
  95 |                     icu::Collator *collate = get_collator(level);
  96 |-                     int len = collate->getSortKey(str,&tmp[0],tmp.size());
  96 |+                     int len = collate->getSortKey(str,tmp.empty()?nullptr:&tmp[0],tmp.size());
```

> Username: jwakely  
> Created_at: 2020-11-20 10:45:12 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527607027  

```diff  
-                    int len = collate->getSortKey(str,tmp.empty()?nullptr:&tmp[0],tmp.size());  
+                    int len = collate->getSortKey(str,tmp.data(),tmp.size());  
```

> Username: OznOg  
> Created_at: 2020-11-20 11:01:35 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527615868  

Yes, I didn't want to use c++11 feature (thus .data()) because I think this code may be use with former norms, but I quite failed (used nullptr :) ). In the end, I think c++03 compat should remain, shouldnt it?

> Username: artyom-beilis  
> Created_at: 2020-11-20 12:18:00 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527653967  

Actually data is undefined as well when the array is empty...  
  
just replace nullptr with 0/NULL and I'll merge, indeed locale supports C++2003 so nullptr is not good there

> Username: jwakely  
> Created_at: 2020-11-20 12:38:09 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527664110  

> Actually data is undefined as well when the array is empty...  
  
No it isn't, that's nonsense. The value is unspecified when the vector is empty, but that doesn't matter. The standard says:  
  
> _Returns:_ A pointer such that `[data(),data() + size())` is a valid range. For a non-empty vector, `data() == &front()`.  
  
Since `size()` is zero, you'll have `[p,p+0)` which is a valid range for **any** pointer value, including null.

> Username: artyom-beilis  
> Created_at: 2020-11-20 12:51:06 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527670586  

Sorry my mistake, now I recall why I didn't use `std::vector::data()` - because it is C++11. It was available as extension in GCC but AFAIR not all c++2003 libraries supported it.  
  
I'm talking about it because the empty vector range was a bug I needed to deal with it several times (and I missed it there in locale)

> Username: jwakely  
> Created_at: 2020-11-20 12:53:58 UTC  
> Updated_at: 2020-11-20 17:06:36 UTC  
> Url: https://github.com/boostorg/locale/pull/60#discussion_r527671951  

The main point of adding `vector::data()` was to solve **exactly this problem**. See https://cplusplus.github.io/LWG/issue464  
  
> It was available as extension in GCC but AFAIR not all c++2003 libraries supported it.  
  
That's correct.


---

## Comment 3

> Username: OznOg  
> Created_at: 2020-11-20 17:07:02 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-731289956  

updated te commit with the NULL instead of nullptr

---

## Comment 4

> Username: OznOg  
> Created_at: 2020-11-27 08:24:23 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-734709281  

Any update on the subject? do you want me to test any other solution?

---

## Comment 5

> Username: OznOg  
> Created_at: 2021-02-25 17:39:48 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-786079061  

ping

---

## Review 6 [Commented]

> Username: jwakely  
> Created_at: 2021-03-03 12:29:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/60#pullrequestreview-602840655  

I think the original patch using `nullptr` was better, because it doesn't require `<cstddef>` for the definition of `NULL`. ~~This file already uses `char16_t` and `char32_t` so cannot be compiled as C++03, so using `nullptr` should be fine. For that matter, using `vector::data()` should also be fine, because it can't be compiled as C++03.~~ **Edit:** Actually the uses of `char16_t` and `char32_t` are guarded by macros, so maybe this file does think it can be compiled in C++03. But if that's the case, it shouldn't be using `uint8_t` as that's not valid before C++11.  
  
Either way, it needs to be fixed somehow. Any of the alternatives is better than crashing due to undefined behaviour.

---

## Comment 7

> Username: hdu-sdlzx  
> Created_at: 2021-12-10 15:05:01 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-991046853  

ping @artyom-beilis @jwakely @Flamefire   
anyone review and merge this PR?

---

## Comment 8

> Username: Flamefire  
> Created_at: 2021-12-10 15:30:35 UTC  
> Updated_at: 2021-12-10 16:58:04 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-991067787  

~~At this point I'd just say: If the string is empty return the empty `tmp` at https://github.com/boostorg/locale/pull/60/files#diff-6b6b0b450f26c89b0a6d407203b2b8cea9c6e661417786980726feed67fb3fa5R93. Avoids this whole issue.~~  
  
Edit: Scratch that:  
> Sort key byte arrays are zero-terminated and can be compared using strcmp().  
  
I.e. the result is always at least 1 byte. Opened https://github.com/boostorg/locale/pull/72 for a better fix.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2021-12-10 20:41:32 UTC  
> Url: https://github.com/boostorg/locale/pull/60#issuecomment-991280052  

Superseded by #72 which also fixes the bug. Thanks for the report and PR!

---
