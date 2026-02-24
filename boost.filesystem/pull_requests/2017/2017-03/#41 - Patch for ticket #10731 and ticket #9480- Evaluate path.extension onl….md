# #41 Patch for ticket #10731 and ticket #9480: Evaluate path.extension onl… [Merged]

> Username: Dani-Hub  
> Created at: 2017-03-14 20:47:54 UTC  
> Updated at: 2017-04-03 12:30:47 UTC  
> Merged at: 2017-04-03 12:29:16 UTC  
> Closed at: 2017-04-03 12:29:16 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41  

…y once, perform (case-insensitive) string comparisons using the wchar_t variants from the CRT to prevent code conversions via path.string() calls.

---

## Comment 1

> Username: sergey-shambir  
> Created_at: 2017-03-16 08:08:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41#issuecomment-286985208  

We also faced with the same problem: unnecessary code convertions cause exceptions for several valid paths, inluding Cyrillic and Chinese paths. This PR fixes wrong exceptions.

---

## Comment 2

> Username: Dani-Hub  
> Created_at: 2017-03-16 08:17:25 UTC  
> Updated_at: 2017-03-18 21:44:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41#issuecomment-286986862  

I should emphasize that the suggested PR uses the same level of case-sensitivity comparison correctness as the existing code. I investigated the matter more deeply and it seems that a better file-name case-sensitity comparison should use CompareStringOrdinal (From Windows Vista on) and LCMapString + binary comparison (For older Windows version), respectively. If interest exists, I'm willing to improve my current PR following that direction.  
I experimented a bit using the suggested case-insensitive file path comparison strategies and the following list the results of that experiment (I used as a boost::timer::auto_cpu_timer over loops of 20000000 calls and each function call got two strings of equal length of 45 characters):  
  
_wcsicmp (zeroterm.)  : 1.266392s wall, 1.250000s user + 0.000000s system = 1.250000s CPU (98.7%)  
CompareStringOrdinal  
(zeroterm.)                 : 1.045033s wall, 1.046875s user + 0.000000s system = 1.046875s CPU (100.2%)  
CompareStringOrdinal  
(+length)                    : 0.811264s wall, 0.812500s user + 0.000000s system = 0.812500s CPU (100.2%)  
LCMapString  
(char-wise, zeroterm.) : 8.225183s wall, 8.234375s user + 0.000000s system = 8.234375s CPU (100.1%)  
LCMapString  
(chunks+length)         : 5.843750s user + 0.000000s system = 5.843750s CPU (99.8%)  
RtlEqualUnicodeString   
(zeroterm.)                  : 2.522009s wall, 2.515625s user + 0.000000s system = 2.515625s CPU (99.7%)  
  
The good news are that when CompareStringOrdinal is available, this function has actually a better performance than the current _wcsicmp call, *and* can be used for mingw, too (So no need for the special handling of _MSC_VER anymore). The bad news are that the replacement call using LCMapString is significantly slower (Albeit I must say that I haven't performed any measurements against realistic data of typical file extensions).  RtlEqualUnicodeString - when available - is a better alternative for CompareStringOrdinal, see  
http://archives.miloush.net/michkap/archive/2007/09/14/4900107.html  
and has a much better performance characteristics than character-wise LCMapString, my revised patch (see below) therefore tries it as second option, so chances should be very low that on realistic systems the LCMapString fallback would be entered.  
  
In addition to the other advantages pointed out above, the new "equal_string_ordinal_ic" function pointer may turn out to be useful when the filesystem library might in the future consider to provide an additional general feature that would allow to perform path comparison functionality according to the underlying filesystem's characteristics.

---

## Comment 3

> Username: Dani-Hub  
> Created_at: 2017-03-19 15:14:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41#issuecomment-287623273  

I investigated more and found that RtlInitUnicodeString did had significant performance impact on RtlEqualUnicodeString. The following table shows an extended experiment using the same conditions as described in the previous comment:  
  
_wcsicmp (zeroterm.):  
 1.282223s wall, 1.281250s user + 0.000000s system = 1.281250s CPU (99.9%)  
LCMapStringW (characterwise):  
 7.970858s wall, 7.968750s user + 0.000000s system = 7.968750s CPU (100.0%)  
LCMapStringW + wmemcmp (chunks):  
 6.186876s wall, 6.171875s user + 0.000000s system = 6.171875s CPU (99.8%)  
CompareStringOrdinal (zeroterm.):  
 1.061088s wall, 1.046875s user + 0.000000s system = 1.046875s CPU (98.7%)  
RtlEqualUnicodeString + RtlInitUnicodeString (zeroterm.):  
 2.586483s wall, 2.562500s user + 0.000000s system = 2.562500s CPU (99.1%)  
RtlEqualUnicodeString + wcslen (zeroterm.):  
 0.873219s wall, 0.875000s user + 0.000000s system = 0.875000s CPU (100.2%)  
CompareStringOrdinal (+length):  
 0.815105s wall, 0.828125s user + 0.000000s system = 0.828125s CPU (101.6%)  
RtlEqualUnicodeString (+length):  
 0.876963s wall, 0.875000s user + 0.000000s system = 0.875000s CPU (99.8%)   
  
It turns out that CompareStringOrdinal is just a wrapper for RtlCompareUnicodeString, see  
  
http://archives.miloush.net/michkap/archive/2007/10/12/5396685.html  
  
so in the end I decided to simplify the previous resolution by ignoring CompareStringOrdinal and simply using RtlEqualUnicodeString + wcslen when RtlEqualUnicodeString is found, falling back to LCMapString (which should rarely ever occur, but since that is a run-time test, we need a fallback). This is what the following commit suggests.

---

## Comment 4

> Username: Dani-Hub  
> Created_at: 2017-03-23 18:58:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41#issuecomment-288826336  

Any chance for considering this patch suggestion for Boost 1.64? If my advanced suggestion looks too far-reaching to you, I would be totally happy with the initial Patch ID 755766a.

---

## Comment 5

> Username: Beman  
> Created_at: 2017-04-03 12:30:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/41#issuecomment-291128876  

Thanks, Daniel!  
  
Fix applied to develop. Will merge to master after tests have cycled.  
  
--Beman

---
