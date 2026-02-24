# #79 Improve compilation speed by taking advantage of memoization [Open]

> Username: jonathanpoelen  
> Created at: 2023-06-20 02:19:59 UTC  
> Updated at: 2026-01-06 23:34:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/79  

I put in bulk the benchmarks and tests that can be found in each commit (issue #77)  
  
(result of `/usr/bin/time --format='%Es - %MK' $compiler ...`)  
  
## `mp_transform_if`  
  
compiler |       gcc-12      |      clang-15  
--|--|--  
before   | 0:00.13s - 60432K | 0:00.16s - 100956K  
after    | 0:00.11s - 47300K | 0:00.16s - 100484K  
  
```cpp  
template<class T> using p = mp_bool<T::value & 1>;  
template<class T> using f = mp_size_t<T::value + 1>;  
template<class I> using test = mp_transform_if<p, f, mp_iota<I>>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_filter`  
  
compiler |       gcc-12      |      clang-15  
--|--|--  
before   | 0:00.16s - 72460K | 0:00.16s - 102068K  
after    | 0:00.14s - 61884K | 0:00.16s - 101828K  
  
```cpp  
template<class T> using p = mp_bool<T::value & 1>;  
template<class I> using test = mp_filter<p, mp_iota<I>>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_drop`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.48s - 192464K | 0:00.48s - 154480K  
after    | 0:00.43s - 172472K | 0:00.46s - 150404K  
  
```cpp  
template<class L> struct f { template<class I> using g = mp_drop<L, I>; };  
template<class I, class L = mp_iota<I>> using test = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_sort`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.46s - 198848K | 0:00.49s - 132632K  
after    | 0:00.42s - 183384K | 0:00.48s - 131748K  
  
```cpp  
template<class I> using test = mp_sort<mp_iota<I>, mp_less>;  
  
using r1 = mp_transform<test, mp_iota_c<25>>;  
```  
  
## `mp_replace_at`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.34s - 134428K | 0:00.33s - 124720K  
after    | 0:00.29s - 116920K | 0:00.32s - 122568K  
  
```cpp  
template<class I> using f = mp_replace_at<mp_iota<I>, I, void>;  
template<class I> using test = mp_transform<f, mp_iota<I>>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_power_set`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:01.35s - 538732K | 0:00.65s - 203924K  
after    | 0:01.02s - 396032K | 0:00.65s - 203080K  
  
```cpp  
template<class I> using test = mp_power_set<mp_iota<I>>;  
  
using r1 = mp_transform<test, mp_iota_c<15>>;  
```  
  
## `mp_map_find`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.29s - 128600K | 0:00.25s - 120604K  
after    | 0:00.22s - 99908K  | 0:00.23s - 116084K  
  
```cpp  
template<class L, class M = mp_transform<mp_list, L>>  
struct f { template<class I> using g = mp_map_find<M, I>; };  
  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_map_update`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.73s - 270224K | 0:00.54s - 156112K  
after    | 0:00.19s - 85004K  | 0:00.29s - 119232K  
  
```cpp  
template<class L, class M = mp_transform<mp_list, L>>  
struct f { template<class I> using g = mp_map_update<M, mp_list<I>, mp_list>; };  
  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<20>>;  
```  
  
## `mp_map_erase`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.52s - 197128K | 0:00.44s - 140780K  
after    | 0:00.47s - 183968K | 0:00.43s - 139336K  
  
```cpp  
template<class L, class M = mp_transform<mp_list, L>>  
struct f { template<class I> using g = mp_map_erase<M, I>; };  
  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<30>>;  
```  
  
## `mp_map_replace`  
  
compiler |       gcc-12       |      clang-15  
--|--|--  
before   | 0:00.46s - 187160K | 0:00.38s - 129104K  
after    | 0:00.21s - 80236K  | 0:00.33s - 123644K  
  
```cpp  
template<class L, class M = mp_transform<mp_list, L>>  
struct f { template<class I> using g = mp_map_replace<M, mp_list<I, I>>; };  
  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<30>>;  
```

---

## Comment 1

> Username: jonathanpoelen  
> Created_at: 2025-11-01 22:04:56 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3476909766  

@pdimov would you be able to take a look, please?

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-11-02 00:08:19 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3476982630  

If you use `fn` as the name of the inner alias, instead of `_f` et al, these helpers will become quoted metafunctions and you'll be able to use the `_q` variants of the algorithms instead of spelling the `::template _f`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-11-03 09:30:40 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3479627356  

This looks good to me. I can only wonder why I failed to look at it two years ago when you submitted it originally; it probably came in an inopportune time when I was too busy.  
  
I see a couple more things. It looks like you started to also optimize `mp_map_replace_impl` by moving the msvc workaround into an ifdef, but then didn't extract `_f` into a helper template.  
  
Also, in the two cases where the `_impl` class is reduced to just having a single `using type = ...` (`mp_transform_if_impl` and `mp_map_erase_impl`), you can just remove the `_impl` class altogether and move the `...` part into the primary alias.  
  
The 1.90 beta cutoff is the day after tomorrow, so I won't be able to merge this now, but to make sure I don't drop the ball again (sorry), please remind me after 1.90 is released.  
  
And thank you for this work.

---

## Comment 4

> Username: jonathanpoelen  
> Created_at: 2025-11-08 18:56:35 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3506801774  

> I see a couple more things. It looks like you started to also optimize `mp_map_replace_impl` by moving the msvc workaround into an ifdef, but then didn't extract `_f` into a helper template.  
  
I went back to msvc because it wasn't working: boostorg/mp11@87719f69d2eee3ae1e4dd686c06507f77b14fead. However, after two years, the logs are no longer available.  
  
> Also, in the two cases where the `_impl` class is reduced to just having a single `using type = ...` (`mp_transform_if_impl` and `mp_map_erase_impl`), you can just remove the `_impl` class altogether and move the `...` part into the primary alias.  
  
Done.  
  
> The 1.90 beta cutoff is the day after tomorrow, so I won't be able to merge this now, but to make sure I don't drop the ball again (sorry), please remind me after 1.90 is released.  
  
I will try to remember :)

---

## Comment 5

> Username: jonathanpoelen  
> Created_at: 2025-11-09 18:47:25 UTC  
> Updated_at: 2025-11-09 19:48:12 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3508674708  

I took the opportunity to go over what I had missed and use a gcc builtin for `make_integer_sequence`.  
  
## `mp_nth_element`  
  
compiler | gcc-15 | clang-20  
-- | -- | --  
before   | 0:00.66s - 206316K | 0:01.77s - 246832K  
after    | 0:00.63s - 192944K | 0:01.76s - 245956K  
  
```cpp  
template<class I> using test  
  = mp_nth_element<mp_iota_c<I::value+1>, I, mp_less>;  
  
using r1 = mp_transform<test, mp_iota_c<30>>;  
```  
  
## `mp_map_find_impl` workaround (GCC)  
  
compiler | gcc-15  
-- | --  
before   | 0:01.65s - 381364K  
after    | 0:00.99s - 213064K  
  
```cpp  
template<class L, class M = mp_transform<mp_list, L>>  
struct f { template<class I> using g = mp_map_find<M, I>; };  
  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<f<L>::template g, L>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_transform` with 5 lists or more  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.21s - 80328K | 0:00.29s - 117756K  
after    | 0:00.20s - 77408K | 0:00.29s - 117468K  
  
```cpp  
template<class I, class L = mp_iota<I>> using test  
  = mp_transform<mp_list, L, L, L, L, L, L, L, L>;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_fill`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.15s - 60740K | 0:00.14s - 94144K  
after    | 0:00.10s - 43504K | 0:00.14s - 94188K  
  
```cpp  
template<class I, class L = mp_iota<I>> using test  
  = mp_fill<L, int>;  
  
using r1 = mp_transform<test, mp_iota_c<100>>;  
```  
  
## `mp_replace_if`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.23s - 76088K | 0:00.37s - 116984K  
after    | 0:00.22s - 75348K | 0:00.35s - 114952K  
  
```cpp  
template<class> using pred = mp_false;  
template<class I, class L = mp_iota<I>> using test  
  = mp_replace_if<L, pred, I>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `mp_replace`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.30s - 90020K | 0:00.48s - 129272K  
after    | 0:00.29s - 89428K | 0:00.46s - 126580K  
  
```cpp  
template<class I, class L = mp_iota<I>> using test  
  = mp_replace<L, I, I>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `mp_remove`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.32s - 91276K | 0:00.69s - 156512K  
after    | 0:00.31s - 90684K | 0:00.67s - 157560K  
  
```cpp  
template<class I, class L = mp_iota<I>> using test  
  = mp_remove<L, I>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `mp_sliding_fold`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.23s - 79084K | 0:00.48s - 135100K  
after    | 0:00.22s - 77748K | 0:00.46s - 132436K  
  
```cpp  
template<class I, class L = mp_iota<I>> using test  
  = mp_list<  
      mp_sliding_fold<L, mp_int<2>, mp_plus>,  
      mp_sliding_fold<L, mp_int<2>, mp_max>  
    >;  
  
using r1 = mp_transform<test, mp_iota_c<50>>;  
```  
  
## `mp_copy_if`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.21s - 65820K | 0:00.51s - 138048K  
after    | 0:00.21s - 65428K | 0:00.49s - 138540K  
  
```cpp  
template<class> using pred = mp_false;  
template<class I, class L = mp_iota<I>> using test  
  = mp_copy_if<L, pred>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `mp_remove_if`  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.21s - 67308K | 0:00.51s - 137744K  
after    | 0:00.21s - 66956K | 0:00.49s - 138492K  
  
```cpp  
template<class> using pred = mp_true;  
template<class I, class L = mp_iota<I>> using test  
  = mp_remove_if<L, pred>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `mp_map_replace`  
  
compiler |      gcc-15        |     clang-20  
-- | -- | --  
before   | 0:00.39s - 114524K | 0:00.66s - 155888K  
after    | 0:00.38s - 114140K | 0:00.64s - 155184K  
  
```cpp  
template<class I, class L = mp_transform<mp_list, mp_iota<I>>> using test  
  = mp_map_replace<L, mp_list<I,I>>;  
  
using r1 = mp_transform<test, mp_iota_c<120>>;  
```  
  
## `integer_sequence` with gcc  
  
Use the `__integer_pack` builtin  
  
compiler |      gcc-15  
-- | --  
before   | 0:00.14s - 47284K  
after    | 0:00.07s - 37572K  
  
```cpp  
template<class I> using test  
  = make_integer_sequence<int, I::value>;  
  
using r1 = mp_transform<test, mp_iota_c<200>>;  
```  
  
## `integer_sequence` when no builtin is available  
  
The probability that this code will be used is very low since msvc and clang-based compilers use `__make_integer_seq` and gcc uses `__integer_pack`. But it is shorter and eliminates the concatenation phase.  
  
compiler |      gcc-15       |     clang-20  
-- | -- | --  
before   | 0:00.14s - 47220K | 0:00.23s - 107632K  
after    | 0:00.10s - 40944K | 0:00.15s - 95144K  
  
```cpp  
template<class I> using test  
  = make_integer_sequence<int, I::value>;  
  
using r1 = mp_transform<test, mp_iota_c<200>>;  
```

---

## Comment 6

> Username: jonathanpoelen  
> Created_at: 2026-01-06 23:34:39 UTC  
> Url: https://github.com/boostorg/mp11/pull/79#issuecomment-3716732988  

@pdimov A quick reminder for this PR.

---
