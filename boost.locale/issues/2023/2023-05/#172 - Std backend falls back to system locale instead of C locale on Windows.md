# #172 - Std backend falls back to system locale instead of C locale on Windows [Closed]

> Username: Flamefire  
> Created at: 2023-05-22 05:43:50 UTC  
> Updated at: 2023-10-11 17:02:59 UTC  
> Closed at: 2023-10-11 17:02:59 UTC  
> Url: https://github.com/boostorg/locale/issues/172  

The fallback in the std backend on Windows uses `to_windows_name` at https://github.com/boostorg/locale/blob/1dfe26c550cad6abb964c5ea6b178bbb3547ce80/src/boost/locale/std/std_backend.cpp#L109-L111  
  
That is supposed to get a locale name based on the Windows LCID by calling `boost::locale::impl_win::locale_to_lcid`.  
  
However the function does not check for a zero lcid returned by `locale_to_lcid` in case the locale is not found and continues which `GetLocaleInfoA` treats as getting the system default, see https://learn.microsoft.com/en-us/windows/win32/intl/locale-neutral  
  
> Instead, use either [LOCALE_SYSTEM_DEFAULT](https://learn.microsoft.com/en-us/windows/win32/intl/locale-system-default) or [LOCALE_USER_DEFAULT](https://learn.microsoft.com/en-us/windows/win32/intl/locale-user-default). The value of LOCALE_NEUTRAL is 0x0000.  
  
Judging by the initialization at https://github.com/boostorg/locale/blob/1dfe26c550cad6abb964c5ea6b178bbb3547ce80/src/boost/locale/std/std_backend.cpp#L32 I assume that the "C" locale should be used instead.  
  
Is that the correct intention? @artyom-beilis   
  
Additionally: IMO it is at least surprising to  silently fall back to the "C" locale when the correct locale couldn't be generated without any chance to notice this (the `boost::locale::info` facet will still report the requested locale) especially combined with the WinAPI backend not supporting non-UTF-8 locales and always falling back to the "C" locale with UTF-8 encoding when any language with a non-UTF-8 encoding is requested.  
  
I would rather throw an exception in that case: The generator couldn't do as requested -> No good way to continue.  
  
Any objections?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2023-05-22 08:43:16 UTC  
> Updated at: 2023-05-22 08:45:51 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1556796252  

> However the function does not check for a zero lcid returned by locale_to_lcid in case the locale is not found  
  
I think you are right, if lcid returned is 0, "C" locale should be used. It is a bug indeed.  
  
Regarding exception, from what I recall only if for example you request something like `en_US.cp1251` from winapi backend that supports UTF-8 only.  
  
The policy to try to fallback as long as you can degrading to "C" locale that may not be optimal but still functional.  
  
Encoding is a little bit different story, because if converting wide-narrow behaves like ASCII - it is a problem.

---

## Comment 2

> Username: Flamefire  
> Created at: 2023-05-22 13:13:47 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1557201373  

> Regarding exception, from what I recall only if for example you request something like `en_US.cp1251` from winapi backend that supports UTF-8 only.  
  
Ok so in that case (WinAPI with non-UTF-8 locale) you'd throw an exception, correct?  
  
> The policy to try to fallback as long as you can degrading to "C" locale that may not be optimal but still functional.  
  
I don't really see how this is functional. E.g. you request `de_DE`, get a result, then format something e.g. `1024.35` to a string and get `1,024.35` as the result instead of `1 024,35` as would be correct even though the `boost::locale::info` of the returned locale claims it is the German locale.  
  
I'd rather make that an exception too. However I agree that at least the translation stuff would still work, only formatting and parsing is broken. Especially the latter might be very bad when unexpected.  
  
A potential improvement would be to check each backend for support and skip it even if requested, so maybe the iconv backend has a locale ICU does not although the user set ICU as preferred Boost.Locale will use iconv for that locale.

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-05-24 14:53:59 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1561312722  

Encoding has an ever bigger issue: When requesting a non-existing, UTF-8 locale, it will use the classic locale with the `from_wide` fallback, however the classic locale doesn't seem to support Unicode: https://godbolt.org/z/69YPjPG7d

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2023-05-24 14:57:00 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1561318075  

> Encoding has an ever bigger issue: When requesting a non-existing, UTF-8 locale, it will use the classic locale with the `from_wide` fallback, however the classic locale doesn't seem to support Unicode: https://godbolt.org/z/69YPjPG7d  
  
if so, utf-8 facet probably should always be imbued into the locale if it is UTF-8.

---

## Comment 5

> Username: Flamefire  
> Created at: 2023-05-24 14:58:31 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1561321110  

You mean e.g. `utf8_converter`? It is, but the wchar ctype facet doesn't handle unicode for the classic locale.

---

## Comment 6

> Username: artyom-beilis  
> Created at: 2023-05-24 15:10:06 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1561342108  

> You mean e.g. `utf8_converter`? It is, but the wchar ctype facet doesn't handle unicode for the classic locale.  
  
it still works for ASCII range as C loale - so in the sample of you put `x` it is converted properly to `X`  
  
Not ideal but something. Finally corrent codecvt can be provided, so it would be gradual fallback. Formatting may be screwed but text conversion (if somebody actually uses this codecvt) would work.  
  
BTW in cygwin  IIRC there is C.UTF-8 locale

---

## Comment 7

> Username: Flamefire  
> Created at: 2023-05-24 15:25:03 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1561367145  

> BTW in cygwin IIRC there is C.UTF-8 locale  
  
Yes that may work: https://godbolt.org/z/rPc7fdEb8

---

## Comment 8

> Username: Flamefire  
> Created at: 2023-10-11 17:02:59 UTC  
> Url: https://github.com/boostorg/locale/issues/172#issuecomment-1758119775  

After #177 "C.UTF-8" is tried too and the classic locale only used if nothing else worked matching  
  
> The policy to try to fallback as long as you can degrading to "C" locale that may not be optimal but still functional.  
  
I still like the approach of automatically choosing a backend supporting the given locale and/or throwing when the request couldn't be "served" but as it was seemingly never requested it might not be an issue anyway.  
  
Closing this as the problem reported is solved.
