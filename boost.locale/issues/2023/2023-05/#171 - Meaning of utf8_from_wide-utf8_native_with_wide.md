# #171 - [Q] Meaning of utf8_from_wide/utf8_native_with_wide [Closed]

> Username: Flamefire  
> Created at: 2023-05-22 05:31:04 UTC  
> Updated at: 2023-06-15 08:21:41 UTC  
> Closed at: 2023-06-15 08:21:41 UTC  
> Url: https://github.com/boostorg/locale/issues/171  

Due to a recent issue related to the different handling of facet creation depending on `utf8_native_with_wide` etc. (now the enum class `utf8_support`) I wanted to ask for clarification:  
  
`utf8_native` seems to be unused, it is checked for but never set, so it can be removed, can't it?  
  
What is the intended difference between `utf8_native_with_wide` and `utf8_from_wide`?  
  
- Both are the same for `converter`: https://github.com/boostorg/locale/blob/boost-1.48.0/src/std/converter.cpp#LL113C27-L113C73  
- For "numeric" they seem to be almost the same: https://github.com/boostorg/locale/blob/boost-1.48.0/src/std/numeric.cpp#L320-L327 vs https://github.com/boostorg/locale/blob/boost-1.48.0/src/std/numeric.cpp#L338-L345  
- In `numeric.cpp` `from_wide` uses `utf8_time_put_from_wide` instead of `time_put_from_base` where the latter looks like it could have been `std::time_put_byname` similar to many other facets  
- In `codecvt.cpp` `from_wide` creates the `utf8_codecvt`: https://github.com/boostorg/locale/blob/boost-1.48.0/src/std/codecvt.cpp#L30-L32 / https://github.com/boostorg/locale/blame/boost-1.60.0/src/std/codecvt.cpp#L31  
- In `collator.cpp` `from_wide` creates the `utf8_collator_from_wide`: https://github.com/boostorg/locale/blob/boost-1.48.0/src/std/collate.cpp#L77-L81  
  
Current logic seems to be that on Windows `utf8_from_wide` is used otherwise `utf8_native_with_wide` is used and when the requested encoding isn't UTF-8 then `utf8_none` is used on all platforms.  
  
However I'm confused that in `numeric.cpp` the `utf8_*_from_wide` classes are used (except for `time_put`) while the collator uses the `*from_wide` variant only on Windows.  
  
To me it looks like either the `utf8_*_from_wide` classes should always be used (which might be a performance issue due to the required 2 conversions) or the standard classes are enough already.  
  
  
  
So questions (assuming an UTF-8 locale is requested):  
  
- Any reason **not** to always use the `utf8_codecvt` given that it should work for UTF-16 and UTF-32 `wchar_t`s?  
- In which case would `time_put_from_base/std::time_put_byname` fail, that `utf8_time_put_from_wide` avoids?  
- Same for `utf8_collator_from_wide` vs `std::collate_byname`  
  
And (generally): Can `time_put_from_base` be replaced by  `std::time_put_byname`?  
  
Only possible reasoning I can see is:    
`std::locale("foo.UTF-8")` fails but `std::locale("foo")` or `std::locale("Windows-name-of-foo")` works, i.e. the standard library does not support the UTF-8 encoding and that has to be emulated.  
  
Is this correct? In that case we would need `utf8_support::none` (non-UTF-8 locale requested), `utf8_support::native` and `utf8_support::from_wide` for when `std::locale("foo.UTF-8")` works and when it doesn't respectively

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2023-05-22 07:47:24 UTC  
> Updated at: 2023-05-22 07:50:28 UTC  
> Url: https://github.com/boostorg/locale/issues/171#issuecomment-1556709268  

Lets start  
  
- `utf8_none` - basically std backend does nothing only support C locale and ASCII code - useless  
- `utf8_native` - basically std backend works and supports proper UTF-8 functionality. But not wide character support.  
- `utf8_native_with_wide` - means it supports UTF-8 but you can use some wide character functionality for better implementation like Linux gnu C library that supports UTF-8 locales (to certain extent) but can workaround some core issues using wide locale  
- `utf8_native_from_wide` - means there is support of unicode locale but for wide only (MSVC classic case) so you can't use `char` locales but you can derive all your ops from `wchar_t`  
  
Now selection of wide data or ordinary character is done as following:  
  
- Usually std::locale works well with collation on Linux - no need to fix - so unless `from_wide` (on Windows) specified std::collate is used  
- Usually numpunct/money-punct does not work well on Linux because when the punctuation mark isn't ASCII like non-breakable space - that it leads to incorrect UTF-8 sequence - so in this case I prefer to use wide character data since narrow is usually broken (also there is workaround for narrow as well).  
- Time is usually works well with native locale without incorrect UTF-8 issues so native are selected  
- codecvt is usually ok - but when from wide is used alternative implementation is needed  
  
I must admit that virtually 1/2 of std backend designed to handle various issues and incompatibilities between implementations - but that is what Boost.Locale is designed for.  
  
> `utf8_native` seems to be unused, it is checked for but never set, so it can be removed, can't it?  
  
`utf8_native` is not in use because all standard libraries that support locales support wide characters as well at this point.  
  
Technically you can remove it, but it may cover in some problematic cases when wide character is not properly supported by `std::locale` on some systems in future.  
  
So you can mark it "for future use" or remove it. Question is what would happen if some day it is going to be needed.  
  
> Any reason not to always use the `utf8_codecvt` given that it should work for UTF-16 and UTF-32 wchar_ts?  
  
It is an option indeed, since native codecvt should provide same functionality when supported but `utf8_codecvt` as well.  
  
> Same for `utf8_collator_from_wide`  
  
Collator from wide is used only on Windows MSVC when no utf-8 locale is supported. Native narrow character utf-8 locale is much more efficient and usually works well.  
  
> In which case would `time_put_from_base`/`std::time_put_byname` fail, that `utf8_time_put_from_wide` avoids  
  
Basically if std char locale supports time put - it is much more efficient than from wide that is why `char` is preferred and from what I recall there were no failures,because time put much better designed in comparison to num/money punct.  
  
Separators in numeric cases are defined by `char` rather than string while for time formatting there are naturally strings that define months and week days - so it is preferred to use native ones.  
  
Example case when numput from wide is better is NBSP character that I can identify and substitute with space,
