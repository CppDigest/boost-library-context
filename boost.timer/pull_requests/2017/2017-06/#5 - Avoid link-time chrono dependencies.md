# #5 Avoid link-time chrono dependencies [Merged]

> Username: igaztanaga  
> Created at: 2017-06-10 22:00:37 UTC  
> Updated at: 2017-12-15 05:59:49 UTC  
> Merged at: 2017-10-23 12:42:09 UTC  
> Closed at: 2017-10-23 12:42:09 UTC  
> Url: https://github.com/boostorg/timer/pull/5  

timer used to be independent from chrono. The tiny dependency used in the implementation can be isolated defining BOOST_CHRONO_HEADER_ONLY

---

## Comment 1

> Username: Beman  
> Created_at: 2017-10-23 12:42:25 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-338646371  

Thanks,  
  
--Beman

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-12-13 10:32:34 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351350795  

This can cause link errors because not all functions in chrono are correctly inlined. I'd like to fix it for 1.66.0 and I'm inclined to revert this (and then fix chrono in the next release), as it's an easier fix. I posted about it on the mailing list, if you have any opinions, please reply there.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-13 15:47:27 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351431176  

Should probably be reverted for 1.66; but in either case, we need to add tests for using `timer` and `chrono` (a) in the same TU (using either include order) and (b) in two distinct TUs. I'm not sure if header-only `chrono` can link to the non-header-only one.

---

## Comment 4

> Username: danieljames  
> Created_at: 2017-12-13 16:01:04 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351435587  

FWIW I tried adding the missing `BOOST_CHRONO_INLINE` to the appropriate functions and still got a link error with Visual C++. GCC worked okay for the simple example, but there's still an ODR violation because it's sometimes inline, sometimes not.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-13 16:10:09 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351438529  

Hm, `chrono` is being used header-only in `src/cpu_timer.cpp`, not in the `timer` headers. I wonder why switching the include order would fix things in this case then.

---

## Comment 6

> Username: danieljames  
> Created_at: 2017-12-13 16:15:12 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351440105  

One thing I missed before is that the function is a member function of a class which is also declared differently. I haven't tried to work out if that makes a difference.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-12-13 16:16:47 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351440626  

The include order probably affects the library link order, one of which (`timer` before `chrono`) happens to work, the other not.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-12-13 16:17:54 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351441009  

Hm, now either order started working for me.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-12-13 18:05:25 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351473436  

The problem does not seem to be caused by the missing `inline`. I get two duplicate definitions,  
  
```  
1>libboost_timer-vc80-mt-s-x64-1_66.lib(cpu_timer.obj) : error LNK2005: "double __cdecl boost::chrono::chrono_detail::get_nanosecs_per_tic(void)" (?get_nanosecs_per_tic@chrono_detail@chrono@boost@@YANXZ) already defined in libboost_chrono-vc80-mt-s-x64-1_66.lib(chrono.obj)  
1>libboost_timer-vc80-mt-s-x64-1_66.lib(cpu_timer.obj) : error LNK2005: "public: static class boost::chrono::time_point<class boost::chrono::steady_clock,class boost::chrono::duration<__int64,class boost::ratio<1,1000000000> > > __cdecl boost::chrono::steady_clock::now(void)" (?now@steady_clock@chrono@boost@@SA?AV?$time_point@Vsteady_clock@chrono@boost@@V?$duration@_JV?$ratio@$00$0DLJKMKAA@@boost@@@23@@23@XZ) already defined in libboost_chrono-vc80-mt-s-x64-1_66.lib(chrono.obj)  
```  
  
and while `steady_clock::now` is indeed missing `inline`:  
  
```  
steady_clock::time_point steady_clock::now() BOOST_NOEXCEPT  
```  
  
`get_nanosecs_per_tic` is not:  
  
```  
BOOST_CHRONO_INLINE double get_nanosecs_per_tic() BOOST_NOEXCEPT  
```  
  
The compiler can emit an out-of-line definition whenever it likes, so I don't think we can fix this.

---

## Comment 10

> Username: danieljames  
> Created_at: 2017-12-13 18:26:16 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351479110  

I mentioned earlier that I tried adding `BOOST_CHRONO_INLINE` and it didn't help. Making everything always `inline` did, but that might require always including the header which defeats the purpose.

---

## Comment 11

> Username: igaztanaga  
> Created_at: 2017-12-13 23:13:17 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351555587  

Does this happen only on MSVC? All versions? In any case we should revert the change.  
  
In practice, I was expecting that when the translation unit seens an inline function a weak symbol should be defined in cpu_timer.obj and the implementation from chrono.obj should be used (as it should be a strong symbol).

---

## Comment 12

> Username: pdimov  
> Created_at: 2017-12-13 23:43:49 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351561493  

Yes, MSVC, all versions. (I tried just 8.0 and 14.1 but I doubt the rest work.)  
  
I don't think that the MSVC link model has weak symbols. `ld` does but it doesn't matter there because the symbol is always resolved from the first library it's found in, you can't get conflicts between libraries.

---

## Review 13 [Commented]

> Username: viboes  
> Created_at: 2017-12-14 10:03:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/timer/pull/5#pullrequestreview-83449973  

📁 src/cpu_timer.cpp

```diff
  16 |+ // define BOOST_CHRONO_HEADER_ONLY so that chrono dependencies are not
  17 |+ // propagated to library users
  18 |+ #define BOOST_CHRONO_HEADER_ONLY
```

> Username: viboes  
> Created_at: 2017-12-14 10:03:02 UTC  
> Url: https://github.com/boostorg/timer/pull/5#discussion_r156899666  

I believe Boost.Timer shouldn't define BOOST_CHRONO_HEADER_ONLY.  
  
It is up to the user to define it.  
  
This means that Boost.Timer should have something similar to Boost.Chrono so that the users of Boost.Timer can choose to have a Boost.Timer header only librray or not.


---

## Comment 14

> Username: igaztanaga  
> Created_at: 2017-12-14 21:13:09 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351837443  

I don't know if I understand the issue correctly. If timer uses chrono as header only, no other library can use chrono as non-header-only (the reported regression). But now, after reverting the change, no one can link timer and use chrono as a header-only dependency in another library. So in any case an option to use a library as header-only (BOOST_CHRONO_HEADER_ONLY) seems quite fragile.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-12-14 22:25:38 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351855027  

It is fragile. All uses have to be header-only, which I think can only be achieved reliably by introducing a new link type `header` in addition to the current `static`/`dynamic` (as Niall has proposed for CMake).  
  
In practice I don't think optional header-only modes work. A library is either header-only or it isn't, can't be both.

---

## Comment 16

> Username: viboes  
> Created_at: 2017-12-15 05:59:49 UTC  
> Url: https://github.com/boostorg/timer/pull/5#issuecomment-351921153  

Ion, you are right that alll these _HEADER_ONLY library are fragile. A library as Boost.Timer using  another library as Boost.Chrono should provide also a _HEADER_ONLY mode. The default one should be no header only. If the user defines BOOST_CHRONO_HEADER_ONLY, Boost.Timer should fail if  BOOST_TIMER_HEADER_ONLY is not defined.  
  
The user of both (all) libraries have a coherent setting coherent. Either they link with both libraries, either with none.  
  
If the user builds also the libraries other combinations are possible.  
  
If the Boost community prefer to remove the header only mode for Boost.Chrono, I will do. This will be a breaking change.  
  
Remember however that Boost.System IIRC is in the same case.   
  
When Boost.Timer decided to use Boost.Chrono, the dependency on the binary library was introduced.

---
