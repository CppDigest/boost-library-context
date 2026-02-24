# #196 - Crash on macOS 14.0 [Closed]

> Username: ksqsf  
> Created at: 2023-10-08 08:44:58 UTC  
> Updated at: 2024-02-08 18:25:50 UTC  
> Closed at: 2024-02-08 18:15:39 UTC  
> Url: https://github.com/boostorg/locale/issues/196  

On macOS Sonoma 14.0 (23A344), the following snippet crashes due to SIGABRT:  
  
```  
#include <cstdio>  
#include <boost/locale/encoding.hpp>  
  
int main()  
{  
  try {  
    boost::locale::conv::from_utf("……", "gbk", boost::locale::conv::method_type::stop);  
  } catch (boost::locale::conv::conversion_error const& ex) {  
    printf("recovered from conversion_error\n");  
  } catch (boost::locale::conv::invalid_charset_error const& ex) {  
    printf("recovered from invalid_charset_error");  
  }  
}  
```  
  
Backtrace:  
  
```  
(lldb) bt  
* thread #1, queue = 'com.apple.main-thread', stop reason = hit program assert  
    frame #0: 0x00007ff80277e7a6 libsystem_kernel.dylib`__pthread_kill + 10  
    frame #1: 0x00007ff8027b6f30 libsystem_pthread.dylib`pthread_kill + 262  
    frame #2: 0x00007ff8026d5a4d libsystem_c.dylib`abort + 126  
    frame #3: 0x00007ff8026d4d60 libsystem_c.dylib`__assert_rtn + 314  
  * frame #4: 0x00007ffc15707f06 libiconv_std.dylib`_citrus_iconv_std_iconv_convert.cold.1 + 35  
    frame #5: 0x00007ffc15707b3b libiconv_std.dylib`_citrus_iconv_std_iconv_convert + 1263  
    frame #6: 0x00007ff80f620010 libiconv.2.dylib`__bsd_iconv + 52  
    frame #7: 0x000000010074f6fe libboost_locale-mt.dylib`std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> boost::locale::conv::impl::iconverter_base::real_convert<char, char>(char const*, char const*) + 222  
    frame #8: 0x000000010074f0ed libboost_locale-mt.dylib`std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> boost::locale::conv::impl::convert_from<char>(char const*, char const*, char const*, boost::locale::conv::method_type) + 125  
    frame #9: 0x000000010074f05f libboost_locale-mt.dylib`std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> boost::locale::conv::from_utf<char>(char const*, char const*, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, boost::locale::conv::method_type) + 31  
    frame #10: 0x0000000100003c2b a.out`std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> boost::locale::conv::from_utf<char>(text="……", charset="gbk", how=stop) at encoding.hpp:91:20  
    frame #11: 0x0000000100003acd a.out`main at main.cc:7:5  
    frame #12: 0x00007ff80242e3a6 dyld`start + 1942  
(lldb)   
```  
  
Boost version: 1.82.0

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-10-08 10:06:44 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1751983384  

I can't see anything wrong in the code. There have been changes in 1.82 and again in 1.83 which might be related but I doubt they could cause or fix this. It might still be worth trying 1.81 or even older if the same behavior occurs or 1.83 to see if it persists.  
  
In your log I see a failed assertion inside the underlying `iconv` call. It might be wort building Boost in debug mode and inspecting the arguments to `iconv` (i.e. from `boost::locale::conv::impl::iconverter_base::real_convert` to `__bsd_iconv`) to see if they are somehow invalid but to me it looks like they should be correct. So it might as well be an issue with your system iconv.  
  
Building Boost with ICU support might avoid Iconv being used and hence provide a workaround.

---

## Comment 2

> Username: Flamefire  
> Created at: 2023-11-23 11:51:11 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1824294414  

@ksqsf As a similar issue just popped up I reduced your issue to the minimal C code which is (or should be) what is actually executed after all the abstractions of Boost.Locale are stripped off: https://godbolt.org/z/asK4aEesv  
  
Can you compile that code (as C and/or C++) on your system and run it to see if the same issue occurs as you observed with Boost.Locale? You can see the expected behavior in the output pane of godbolt, in particular there is no crash and no failed assertion.  
  
It would also be helpful to know where it crashes exactly. I.e. debug before & after [this call](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L54-L57) which will end up [here](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/util/iconv.hpp#L77) and then (likely) [calling iconv here](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/util/iconv.hpp#L72)     
Things to check would be that the parameters are still as expected and whether it fails on the first call (i.e. non-NULL in/insize) or second call (in/insize are NULL)

---

## Comment 3

> Username: ksqsf  
> Created at: 2023-11-24 03:28:19 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1825105434  

Hi @Flamefire , thanks for your investigation and sorry for the late reply. Your earlier reply somehow slipped my attention...  
  
I can confirm that the C program indeed crashes here:  
  
```  
in_left: 6  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
Assertion failed: (out_left == 64u - 4u), function main, file minimized.c, line 30.  
zsh: abort      ./a.out  
```  
  
So it is an upstream problem. With that said, I cannot reproduce the same crash anymore: I upgraded the system once and also upgraded boost to 1.83.0. I worked around this problem earlier, so I kind of lost track of why...  
  
Let me know if there's more info you want. Otherwise, feel free to close this report.

---

## Comment 4

> Username: Flamefire  
> Created at: 2023-11-24 10:42:34 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1825477150  

Yes that looks indeed broken: It should not convert the 2 Unicode codepoints to only 2 characters but to 4 instead, in particular "A1AD". However I have found https://www.fileformat.info/info/charset/GBK/encode.htm where "…" is NEL (U+0085) marked as "unmappable" but on https://www.fileformat.info/info/charset/GBK/list.htm it is HORIZONTAL ELLIPSIS (U+2026) and encoded as A1AD so it might be system dependent which one is used. I made a small C program to show the encoded version: https://godbolt.org/z/Yz6Mn157v , but in your case it already is using U+2026 as the UTF-8 version is 6 bytes long.  
  
As the error is different (it doesn't crash in `__bsd_iconv` but "just" fails the assertion) I'd like to ask you to test the [modified example](https://godbolt.org/z/14eaPefsW) (which tests a bit more which could be relevant) and post the full output.  
  
FTR: I don't know why that iconv considers U+0085 an invalid sequence and returns EILSEQ (@artyom-beilis any idea?)  
  
@ksqsf  In the upcoming Boost release you can use `boost.locale.iconv=off` when building boost to disable iconv completely which bypasses this issue.

---

## Comment 5

> Username: ksqsf  
> Created at: 2023-11-24 11:00:48 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1825504586  

The full output of the modified example is:  
  
```  
E2BIG=7  
EILSEQ=92  
EOPNOTSUPP=102  
EINVAL=22  
  
Original: \E2\80\A6\E2\80\A6  
in_left: 6  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
modified.c:36 Test FAILED: out_left == 64u - 4u  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
modified.c:47 Test FAILED: out_left == 64u - 4u  
  
U+0085: \C2\85\C2\85  
in_left: 4  
res: 4294967295  
errno: 92  
in_left: 4  
out_left: 64  
  
modified.c:33 Test FAILED: res == 0u  
modified.c:34 Test FAILED: errno == 0  
modified.c:35 Test FAILED: in_left == 0u  
modified.c:36 Test FAILED: out_left == 64u - 4u  
res: 0  
errno: 92  
in_left: 4  
out_left: 64  
  
modified.c:45 Test FAILED: errno == 0  
modified.c:46 Test FAILED: in_left == 0u  
modified.c:47 Test FAILED: out_left == 64u - 4u  
  
U+2026: \E2\80\A6\E2\80\A6  
in_left: 6  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
modified.c:36 Test FAILED: out_left == 64u - 4u  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
modified.c:47 Test FAILED: out_left == 64u - 4u  
```

---

## Comment 6

> Username: Flamefire  
> Created at: 2023-11-24 16:30:28 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1825887266  

Ok thank you. So it shows the same EILSEQ error for U+85 as on Linux but encodes U+2026 as a question mark (U+3F) likely as a kind of replacement. This is IMO still wrong and should be reported to Apple. However the program aborting with an assert at `libiconv_std.dylib``_citrus_iconv_std_iconv_convert.cold.1 + 35` was not reproduced. This makes me assume the original crash is fixed by Apple in one of the system upgrades you mentioned

---

## Comment 7

> Username: artyom-beilis  
> Created at: 2023-11-24 21:36:36 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1826097508  

This way or other it is important to notice that there are native Mac OS X version of iconv and there is GNU Iconv usually does not have problems.

---

## Comment 8

> Username: Flamefire  
> Created at: 2024-02-08 18:15:39 UTC  
> Updated at: 2024-02-08 18:25:50 UTC  
> Url: https://github.com/boostorg/locale/issues/196#issuecomment-1934689343  

It looks like this is indeed an issue with macOS 14.1/iOS 17.1 and fixed in 14.2/17.2 respectively as per https://github.com/d99kris/nmail/issues/150#issuecomment-1879929847  
  
I also created #218 to detect the related issue #206 and handle it with an exception instead of an infinite loop. CI also shows the (wrong) replacement by U+3F we see here:  
  
```  
-- gbk  
----- [IConv]  
----- [IConv]  
Error in line 103: convPtr->convert(source) == target  
---- [??] != [����]  
-- ISO-2022-CN  
----- [IConv]  
----- [IConv]  
Failed with exception St13runtime_error(No progress, IConv is faulty!)  
```  
  
I hence close this issue as an OS bug which has since been fixed by the OS vendor especially as we can't do anything besides disabling iconv via `boost.locale.iconv=off`
