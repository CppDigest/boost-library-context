# #2 Suppress MSVC warning C4800 caused by static_cast<bool>(unsigned) [Closed]

> Username: tklauser  
> Created at: 2016-02-17 14:26:44 UTC  
> Updated at: 2022-04-16 14:59:30 UTC  
> Closed at: 2022-04-16 12:30:39 UTC  
> Url: https://github.com/boostorg/crc/pull/2  

The static_cast<bool> of an unsigned causes the following warning with  
MSVC:  
  
  warning C4800: "'int' : forcing value to bool 'true' or 'false' (performance warning)  
  
Suppress the warning for the cast in question as is done in other places  
of Boost.  
  
This fixes https://svn.boost.org/trac/boost/ticket/9198

---

## Comment 1

> Username: SpareSimian  
> Created_at: 2019-03-21 20:49:09 UTC  
> Url: https://github.com/boostorg/crc/pull/2#issuecomment-475396605  

This seems to have returned in 1.69.0 at line 569:  
  
boost_1_69_0\boost/crc.hpp(569): warning C4800: 'unsigned int': forcing value to bool 'true' or 'false' (performance warning)  
  
It works fine for me after making the suggested change in the old trac system.

---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2019-06-20 12:34:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/crc/pull/2#pullrequestreview-252260989  

📁 include/boost/crc.hpp

```diff
1779 |+ #pragma warning(disable:4800)
1780 |+ #endif
1781 |         process_bit( static_cast<bool>(bits & high_bit_mask) );
```

> Username: Kojoley  
> Created_at: 2019-06-20 12:34:08 UTC  
> Updated_at: 2019-06-21 09:43:09 UTC  
> Url: https://github.com/boostorg/crc/pull/2#discussion_r295783593  

`(bits & high_bit_mask) > 0u` is a much better fix for this warning

> Username: jeking3  
> Created_at: 2019-06-20 14:15:32 UTC  
> Updated_at: 2019-06-21 09:43:09 UTC  
> Url: https://github.com/boostorg/crc/pull/2#discussion_r295826845  

It would also be nice to actually enable the CI providers for this repository.

> Username: tklauser  
> Created_at: 2019-06-21 09:43:35 UTC  
> Updated_at: 2019-06-21 09:43:36 UTC  
> Url: https://github.com/boostorg/crc/pull/2#discussion_r296168829  

Thanks @Kojoley, adjusted accordingly.


---

## Comment 3

> Username: glenfe  
> Created_at: 2022-04-16 12:30:39 UTC  
> Url: https://github.com/boostorg/crc/pull/2#issuecomment-1100653390  

Should be addressed in the upcoming 1.80 release by 96daae9fcdf965298077ed12a9304ea24713bb40

---
