# #70 - change ICU_LINK configuration [Closed]

> Username: SSE4  
> Created at: 2021-10-06 12:25:42 UTC  
> Updated at: 2022-10-19 12:25:14 UTC  
> Closed at: 2022-10-19 12:25:14 UTC  
> Url: https://github.com/boostorg/locale/pull/70  

closes: #55   
  
this is basically a port of the following `boost.regex` commit: https://github.com/boostorg/regex/commit/fc4dc17dc745a9fc4ab1849cef3710354a6b7782  
  
summary:  
- `ICU_LINK` is deprecated  
- added more granular variables to control ICU library names (`ICU_ICUUC_NAME`, `ICU_ICUDT_NAME`, `ICU_ICUIN_NAME`)  
- static ICU is allowed (it's already allowed in regex, no reason to forbid it)  
  
/cc @grafikrobot

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2021-10-06 13:18:53 UTC  
> Url: https://github.com/boostorg/locale/pull/70#issuecomment-936221930  

Are you sure about it: https://github.com/boostorg/locale/pull/70/files#diff-54876b48ff1d36d4e9f8ff25a1cb2c9ff33db706dc6eb8b82ca63ae2953daf2dL19  
  
    - #error "Mixing ICU with a static runtime doesn't work"  
    +// #error "Mixing ICU with a static runtime doesn't work"  
  
Unlike regex it needs icudata with all locale information. I recall some issues with it. That without dll it does not have relevant data.  
  
Have you tested it?  
  
Also can somebody help me reviewing the b2 files. I really barely understand them

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2021-10-06 13:43:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/locale/pull/70#pullrequestreview-772721874  

📁 build/Jamfile.v2

```diff
 125 |     searched-lib icudt : :  <search>$(ICU_PATH)/lib
 126 |                             <name>icudata
 127 |                             <link>shared
```

> Username: grafikrobot  
> Created_at: 2021-10-06 13:43:17 UTC  
> Updated_at: 2021-10-06 13:43:25 UTC  
> Url: https://github.com/boostorg/locale/pull/70#discussion_r723288810  

To be consistent should use either `lib` or `search-lib` targets. The regex build uses `lib` consistently.

> Username: SSE4  
> Created_at: 2021-10-06 13:46:49 UTC  
> Url: https://github.com/boostorg/locale/pull/70#discussion_r723293415  

okay, I'll try to change that


---

## Comment 3

> Username: SSE4  
> Created_at: 2021-10-06 13:48:58 UTC  
> Url: https://github.com/boostorg/locale/pull/70#issuecomment-936270843  

> Have you tested it?  
  
I've just run some tests for shared and static ICU builds, both failed...  
  
[test_shared.log](https://github.com/boostorg/locale/files/7294433/test_shared.log)  
[test_static.log](https://github.com/boostorg/locale/files/7294434/test_static.log)  
  
I can try to fix them, but only with some guidance, I am not very familiar with boost locale

---
