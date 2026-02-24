# #206 - locale::conv::from_utf freezing on macOS14 and iOS17 because iconv library [Closed]

> Username: daniboybye  
> Created at: 2023-11-20 11:24:13 UTC  
> Updated at: 2024-02-12 17:13:04 UTC  
> Closed at: 2024-02-12 17:13:03 UTC  
> Url: https://github.com/boostorg/locale/issues/206  

- boost version 1.82  
- Xcode version 15.0.1 (15A507)  
- macOS 14.0 or iOS 17.1  
- calling the function with following arguments boost::locale::conv::from_utf("实", "ISO-2022-CN") and using the system iconv  causes a freeze  
- example project: https://mobidrive.com/sharelink/z/5D06wKTBZOmOpRVfwpiAaj3z7lfzi4oZgsAl5botBygx

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-11-21 11:06:48 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1820705131  

This looks similar to #196 as it happens with IConv on macOS 14. As I don't have access to a Mac I can't test it there to see what the issue might be. It looks like Apple has changed the iconv library on macOS 14 to be incompatible with the GNU version, see e.g. https://developer.apple.com/forums/thread/739533  
  
I'm unable to find a documentation for libiconv on macOS 14 where the behavior is specified such that it can be compare with the other iconv implementations such as for [GNU](https://www.gnu.org/software/libiconv/documentation/libiconv-1.13/iconv.3.html)   
  
So I'd need your help here:  
- Can you find a documentation of that library on your system?  
- Can you debug why it hangs? I.e. check https://github.com/boostorg/locale/blob/c5314a857c5af029ced242820ef62deeec065b1d/src/boost/locale/encoding/iconv_converter.hpp#L50-L59  
  
On GNU libiconv for your input we have `in_left=3` before, `in_left=0` after the `conv` call and `res=0` & `output_count=7`. What are the values on your system?  
  
A workaround would be to not use iconv but ICU by disabling iconv which requires a change to the B2 build file: #207

---

## Comment 2

> Username: daniboybye  
> Created at: 2023-11-21 13:31:43 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1820933177  

In my systems:  
on first pass: in_left=1 before, in_left=0 after, res=0, output_count=1  
on second pass: in_left=0 before, in_left=0 after, res=0, output_count=0  
on third pass: in_left=3 before, in_left=3 after, res=-1, output_count=0  
and we can't read more after that position

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-11-21 15:44:32 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1821177016  

Thanks for testing! I've noticed that I send you the code position for the develop branch, not 1.82. I assume you tested 1.82? I'll use that below but should be trivial to adapt if not.  
  
 Let me think loud:  
  
>  on first pass: in_left=1 before, in_left=0 after, res=0, output_count=1  
  
That looks wrong. If `"实"` was in UTF-8 it would be 3 bytes, not one. So I assume you saved the file in some locale specific encoding where that character can be represented in 1 Byte and hence is **not** UTF-8 and hence calling `from_utf` will not produce the expected result in any case. However it also shouldn't hang, so let's continue:  
  
>  on second pass: in_left=0 before, in_left=0 after, res=0, output_count=0  
  
`in_left=0` should set [`state = unshifting`](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L51-L52) and as after the next call [`res != -1`](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L71) we'll set [`state = done`](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L95-L96) which will [exit the loop](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L45)  
  
>  on third pass: in_left=3 before, in_left=3 after, res=-1, output_count=0  
  
First: How could a third pass happen if `state=done`? And why do we have `in_left=3` now as we should have at the start? Maybe your "first pass" is on something else and the "third pass" is actually the first pass on your string? Can you verify this?  
  
However the `res=-1` should be checked [here](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L71) and depending on `err` should continue the loop if `EILSEQ or EINVAL` after [incrementing begin](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L78), just continue for [`E2BIG`](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L85) or [exit the loop](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L92) for an unknown errno.  
  
So what exactly did you mean by "causes a freeze" in the initial report or that last part?:  
  
>  and we can't read more after that position

---

## Comment 4

> Username: daniboybye  
> Created at: 2023-11-22 12:05:05 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1822646950  

My mistake, you're right. First pass is in_left=3 before, in_left=3 after, res=-1, output_count=0.  
Err is 7(E2BIG) and state is normal.

---

## Comment 5

> Username: Flamefire  
> Created at: 2023-11-22 12:43:34 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1822703653  

Ah that explains the infinite loop: It continues the loop without consuming or producing anything so it will do the exact same thing over and over again. I can add a check for that (so it returns an empty string instead) but the system iconv implementation still looks broken: E2BIG should be raised if ["There is not sufficient room at `*outbuf.`"](https://developer.apple.com/library/archive/documentation/System/Conceptual/ManPages_iPhoneOS/man3/iconv.3.html)  
  
But we pass a valid pointer as `outbuf` and a size of 64 as `out_left`. How would that be not enough room to consume even a single byte? Can you verify that at https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/util/iconv.hpp#L77 `out` points to `result` and `outsize` to "64"?  
  
Maybe also compare the iconv manpage on your system (`man "iconv(3)"` on a shell) to the above linked to see if that has any differences which might provide hints.  
  
If not I can only document that "IConv on macOS 14+ is broken and should be disabled" which isn't a great solution especially as we cannot detect this easily at buildtime as the interfaces are all there.  
  
CC @artyom-beilis if he has any ideas left.

---

## Comment 6

> Username: daniboybye  
> Created at: 2023-11-22 12:52:01 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1822715990  

Adding check for E2BIG is not solution because we can have more symbols after Chinese one.

---

## Comment 7

> Username: artyom-beilis  
> Created at: 2023-11-22 12:57:43 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1822725219  

I suggest lets make trivial C example that reporduces the problem.   
  
> In my systems: on first pass: in_left=1 before, in_left=0 after, res=0, output_count=1 on second pass: in_left=0 before, in_left=0 after, res=0, output_count=0 on third pass: in_left=3 before, in_left=3 after, res=-1, output_count=0 and we can't read more after that position  
  
What I don't understand why after `in_left = 0 ` and res = 0 the loop is not breaking:  https://github.com/boostorg/locale/blob/c5314a857c5af029ced242820ef62deeec065b1d/src/boost/locale/encoding/iconv_converter.hpp#L78C17-L79C27  
  
On first call it consumes 3 bytes and outputs one char. Next returns res = 0 - which basically means we need to exit since we are in unshifting state.  
  
I really don't understand how we get there

---

## Comment 8

> Username: Flamefire  
> Created at: 2023-11-22 13:13:14 UTC  
> Updated at: 2023-11-22 13:14:18 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1822748384  

> Adding check for E2BIG is not solution because we can have more symbols after Chinese one.  
  
I meant to add a check [here](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L85) if any progress was made, i.e. `in_left` changed or `output_count > 0` and only then continue. Otherwise either abort with an error that the implementation is broken or continue with the next input character like [here](https://github.com/boostorg/locale/blob/boost-1.82.0/src/boost/locale/encoding/iconv_codepage.ipp#L77-L80)  
  
> What I don't understand why after `in_left = 0 ` and res = 0 the loop is not breaking: https://github.com/boostorg/locale/blob/c5314a857c5af029ced242820ef62deeec065b1d/src/boost/locale/encoding/iconv_converter.hpp#L78C17-L79C27  
>   
> On first call it consumes 3 bytes and outputs one char. Next returns res = 0 - which basically means we need to exit since we are in unshifting state.  
  
@artyom-beilis See https://github.com/boostorg/locale/issues/206#issuecomment-1822646950 The first 2  were something different.  
  
But yes a simple C code demonstrating the issue in Iconv and throwing at Apple support might be an idea. I wrote a simplified code: https://godbolt.org/z/oY6Th5Gh5 @daniboybye Can you try to compile and run that code on your system and see if that fails? If it does (which it should) please submit it to Apple support if you can.

---

## Comment 9

> Username: daniboybye  
> Created at: 2023-11-23 14:44:14 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1824552181  

I confirm that this example demonstrates the problem and will add it to my report to Apple.

---

## Comment 10

> Username: Flamefire  
> Created at: 2023-11-24 10:52:38 UTC  
> Updated at: 2023-11-24 10:52:55 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1825494430  

Thank you. Please let us know if you get any new information.  
  
> I confirm that this example demonstrates the problem and will add it to my report to Apple.  
  
Can you post the output or how you are sure it is exactly this problem? I.e. no input consumed (`in_left == 3` && `out_left == 64`), `res=-1` with `errno=E2BIG`?  
  
It occured to me that on MacOS it might be using the FreeBSD version. Could you run https://godbolt.org/z/98aah1n51 (and optionally https://godbolt.org/z/14eaPefsW for the related issue) on your system and post the output please? It also contains more printed

---

## Comment 11

> Username: daniboybye  
> Created at: 2023-11-26 20:33:06 UTC  
> Updated at: 2023-11-26 20:33:42 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1826889305  

My output for [https://godbolt.org/z/98aah1n51](https://godbolt.org/z/98aah1n51)  
  
E2BIG=7  
EILSEQ=92  
EOPNOTSUPP=102  
EINVAL=22  
  
Original: \E5\AE\9E  
in_left: 3  
res: 4294967295  
errno: 7  
in_left: 3  
out_left: 64  
  
line:33 Test FAILED: res == 0u  
line:34 Test FAILED: errno == 0  
line:35 Test FAILED: in_left == 0u  
line:36 Test FAILED: out_left == 64u - 7u  
res: 0  
errno: 7  
in_left: 3  
out_left: 64  
  
line:45 Test FAILED: errno == 0  
line:46 Test FAILED: in_left == 0u  
line:47 Test FAILED: out_left == 64u - 8u  
  
--------------------------------------  
  
[https://godbolt.org/z/14eaPefsW](https://godbolt.org/z/14eaPefsW )  
  
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
line:36 Test FAILED: out_left == 64u - 4u  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
line:47 Test FAILED: out_left == 64u - 4u  
  
U+0085: \C2\85\C2\85  
in_left: 4  
res: 4294967295  
errno: 92  
in_left: 4  
out_left: 64  
  
line:33 Test FAILED: res == 0u  
line:34 Test FAILED: errno == 0  
line:35 Test FAILED: in_left == 0u  
line:36 Test FAILED: out_left == 64u - 4u  
  
res: 0  
errno: 92  
in_left: 4  
out_left: 64  
  
line:45 Test FAILED: errno == 0  
/line:46 Test FAILED: in_left == 0u  
line:47 Test FAILED: out_left == 64u - 4u  
  
U+2026: \E2\80\A6\E2\80\A6  
in_left: 6  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
line:36 Test FAILED: out_left == 64u - 4u  
res: 0  
errno: 0  
in_left: 0  
out_left: 62  
\3F\3F  
line:47 Test FAILED: out_left == 64u - 4u

---

## Comment 12

> Username: Flamefire  
> Created at: 2024-02-08 18:17:18 UTC  
> Url: https://github.com/boostorg/locale/issues/206#issuecomment-1934691724  

It looks like this is indeed an issue with macOS 14/iOS 17 and fixed in 14.2/17.2 respectively as per https://github.com/d99kris/nmail/issues/150#issuecomment-1879929847  
  
#218 will close this issue by throwing and exception instead of freezing when the issue is detected
