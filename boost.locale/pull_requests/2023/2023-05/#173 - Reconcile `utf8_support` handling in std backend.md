# #173 Reconcile `utf8_support` handling in std backend [Merged]

> Username: Flamefire  
> Created at: 2023-05-24 10:49:44 UTC  
> Updated at: 2023-06-15 08:21:43 UTC  
> Merged at: 2023-06-15 08:21:39 UTC  
> Closed at: 2023-06-15 08:21:40 UTC  
> Url: https://github.com/boostorg/locale/pull/173  

Document the meaning of the `utf8_support` enum and refactor/change usages to be consistent and logical.     
Add comments where required.  
  
- Remove the unused `utf8_support::native` which is only checked for but never set  
- Rename `native_with_wide` to `native` after that is now free for use. This makes the meaning clear as there is no `native_without_wide`.  
- Move the Windows-codecvt workaround directly where it matters/is used to not abuse `utf8_support` for this which was confusing  
  
Closes #171   
  
@artyom-beilis Please do a quick check that I didn't miss anything or misunderstood something you wrote in #171

---

## Review 1 [Commented]

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:17:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/173#pullrequestreview-1441581738  

📁 src/boost/locale/std/collate.cpp

```diff
  56 |-             BOOST_LOCALE_END_CONST_CONDITION
  57 |-             return key;
  39 |+             return conv::utf_to_utf<char>(wkey);
```

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:17:39 UTC  
> Updated_at: 2023-05-24 11:17:40 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203926744  

This is incorrect.  
  
I explain. transform returns sequence of numeric units that is lexicographical sort aligned with the collation order.  
  
  
There is no guarantee whatsoever if the actual encoding is valid UTF-8/16/32. The only guarantee is sort order.  
  
That is why this "wired" conversion is used.

> Username: Flamefire  
> Created_at: 2023-05-24 11:24:54 UTC  
> Updated_at: 2023-05-24 11:24:55 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203936627  

I see, I actually wanted to ask you directly about that as I was wondering if that was only because e.g. `utf_to_utf` didn't exist when this was written or it wasn't known that UTF-8 is already lexicographically sortable,  
I'll revert this part

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:28:56 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203942116  

Yes it took me several minutes to find out why have I done it this way (ohhh I need to comment stuff better)  
  
Thanks!

> Username: Flamefire  
> Created_at: 2023-06-12 07:37:03 UTC  
> Updated_at: 2023-06-12 07:38:31 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1226213740  

I replaced this now by more general code which basically does only endian conversion of the char type so works for whatever the wchar-size is. Also added a comment, should be much easier to understand and might be fast than the original variant using part-byte bit sizes: https://github.com/boostorg/locale/pull/173/commits/0991420a362df5290fd55a42b4a2c9eb399170b1  
  
Also have to use this to work around a bug in the std-collate on Windows with UTF-8 -.- https://github.com/boostorg/locale/pull/173/commits/90125f7babc412eccd1e2fd249b0d88f2b65d9b1


---

## Review 2 [Commented]

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:20:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/173#pullrequestreview-1441586545  

📁 src/boost/locale/std/numeric.cpp

```diff
  20 | namespace boost { namespace locale { namespace impl_std {
  21 | 
  22 |-     template<typename CharType>
```

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:20:49 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203931034  

why timeput from base was removed?  
  
It is used in two cases  
  
1. non-UTF-8 locale  
2. it is much more optimal than one from wide.

> Username: Flamefire  
> Created_at: 2023-05-24 11:22:51 UTC  
> Updated_at: 2023-05-24 11:22:52 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203933888  

Because it is the same as `std::timeput_byname` isn't it? See https://github.com/boostorg/locale/pull/173/files#diff-007e19c0c340572d5ed484dbad7e51795304b6a7fac45e631076284da5daf6e1R223

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:27:00 UTC  
> Updated_at: 2023-05-24 11:27:01 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1203939510  

Ohh yes. I missed the part you install it directly!

> Username: Flamefire  
> Created_at: 2023-06-12 07:31:07 UTC  
> Updated_at: 2023-06-12 07:31:08 UTC  
> Url: https://github.com/boostorg/locale/pull/173#discussion_r1226206601  

I had to add it back after I found why this is needed: `timeput_byname` is not self-contained. It uses the locales names for e.g. weekdays and format. Doesn't really make sense as that IMO is what this facet should be for but well: https://github.com/boostorg/locale/pull/173/commits/29f87ebed71513a892fc27d41cc390eed2d1d673


---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2023-05-24 11:22:33 UTC  
> Url: https://github.com/boostorg/locale/pull/173#issuecomment-1560939456  

I added few comments. `:-)`

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2023-06-02 12:16:18 UTC  
> Updated_at: 2023-06-14 17:48:55 UTC  
> Url: https://github.com/boostorg/locale/pull/173#issuecomment-1573642721  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#173](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ac05f1d) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/aa58a4263782820ab1a1945c8148f785376f6629?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (aa58a42) will **increase** coverage by `0.37%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/173/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #173      +/-   ##  
===========================================  
+ Coverage    92.32%   92.69%   +0.37%       
===========================================  
  Files          112      112                
  Lines         9878     9820      -58       
===========================================  
- Hits          9120     9103      -17       
+ Misses         758      717      -41       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/std/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29kZWN2dC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/std/collate.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29sbGF0ZS5jcHA=) | `88.09% <100.00%> (+13.73%)` | :arrow_up: |  
| [src/boost/locale/std/converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29udmVydGVyLmNwcA==) | `86.36% <100.00%> (ø)` | |  
| [src/boost/locale/std/numeric.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvbnVtZXJpYy5jcHA=) | `91.66% <100.00%> (+14.90%)` | :arrow_up: |  
| [src/boost/locale/std/std\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvc3RkX2JhY2tlbmQuY3Bw) | `91.52% <100.00%> (-0.08%)` | :arrow_down: |  
| [test/test\_std\_collate.cpp](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb2xsYXRlLmNwcA==) | `98.00% <100.00%> (+6.51%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [aa58a42...ac05f1d](https://app.codecov.io/gh/boostorg/locale/pull/173?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
