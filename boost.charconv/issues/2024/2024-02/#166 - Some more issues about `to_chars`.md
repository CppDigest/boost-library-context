# #166 - Some more issues about `to_chars` [Open]

> Username: jk-jeon  
> Created at: 2024-02-20 02:05:36 UTC  
> Updated at: 2024-10-24 00:59:07 UTC  
> Url: https://github.com/boostorg/charconv/issues/166  

1. It seems that `std::to_chars` returns `errc::value_too_large` rather than `errc::result_out_of_range`. Is this simply a mistake or do you have a reason for using `errc::result_out_of_range` instead? In the code I edited I used `errc::result_out_of_range` for consistency.  
  
3. `to_chars` with only buffer & value, with the format param as well, and with the precision param as well, all should be separate overloads. The reason why the first and the second should be separate overloads is because the first one should behave differently from the second one with `fmt == chars_format::general`; it needs to select whatever representation that is shortest, which does not always need to be equal to `chars_format::general`'s output. The reason why the second and the third should be separate overloads is because by the spec, `precision` being negative should be treated as if `precision == 6`, because the spec of `std::printf` says that negative precision is ignored, which means it should fall back to the default, which is 6. I guess this is quite stupid, but well, it seems that's how the spec is written anyway.  
  
EDIT: Ah, forgot to mention. When fixed format is chosen for the shortest representation, the output of Dragonbox may not be the correctly rounded one, because there can be trailing zeros in the integer part. (See https://github.com/fmtlib/fmt/issues/3649 for a related discussion in libfmt.)

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-20 05:26:37 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1953510030  

> It seems that `std::to_chars` returns `errc::value_too_large` rather than `errc::result_out_of_range`. Is this simply a mistake or do you have a reason for using `errc::result_out_of_range` instead?  
  
That's how `std::to_chars` is specified, and it's consistent with POSIX error handling. ERANGE is when a result can't fit in the range of the numeric output; EOVERFLOW is when there's a buffer overflow.

---

## Comment 2

> Username: jk-jeon  
> Created at: 2024-02-20 05:54:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1953532569  

@pdimov I mean, `boost::charconv::to_chars` returns `errc::result_out_of_range` while `std::to_chars` seems to be supposed to return `errc::value_too_large`. Is there a reason for this divergence?

---

## Comment 3

> Username: pdimov  
> Created at: 2024-02-20 06:05:02 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1953542624  

That should be a bug, then.

---

## Comment 4

> Username: mborland  
> Created at: 2024-02-20 07:50:18 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1953648980  

> Is there a reason for this divergence?  
  
No; it will be fixed by linked PR.

---

## Comment 5

> Username: mborland  
> Created at: 2024-02-20 08:09:13 UTC  
> Updated at: 2024-02-20 10:18:52 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1953673662  

> 2. `to_chars` with only buffer & value, with the format param as well, and with the precision param as well, all should be separate overloads. The reason why the first and the second should be separate overloads is because the first one should behave differently from the second one with `fmt == chars_format::general`; it needs to select whatever representation that is shortest, which does not always need to be equal to `chars_format::general`'s output.   
  
I believe the difference is between formatting with 1e-4 as the crossover point between fixed and scientific like in your last issue right? Since the goal is the absolute minimum number of characters? https://godbolt.org/z/bnscGEbMn  
  
>The reason why the second and the third should be separate overloads is because by the spec, `precision` being negative should be treated as if `precision == 6`, because the spec of `std::printf` says that negative precision is ignored, which means it should fall back to the default, which is 6. I guess this is quite stupid, but well, it seems that's how the spec is written anyway.  
>  
  
That does seem to be the case: https://godbolt.org/z/6xPMKeTcM

---

## Comment 6

> Username: jk-jeon  
> Created at: 2024-02-21 09:00:22 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-1956179016  

>I believe the difference is between formatting with 1e-4 as the crossover point between fixed and scientific like in your last issue right? Since the goal is the absolute minimum number of characters? https://godbolt.org/z/bnscGEbMn  
  
Yeah, `1e-4` should be printed as `1e-4` because it's shorter than `0.0001`, and similarly `1e-3` is preferred over `0.001`. But those are "easy" cases. Real headaches are the cases like the ones described in the fmt issue I linked (like `123456792.0f`, in case it was not clear).

---

## Comment 7

> Username: gpeterhoff  
> Created at: 2024-10-17 21:43:18 UTC  
> Updated at: 2024-10-17 21:46:42 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-2420655891  

FP128 still does not work  
  
see https://godbolt.org/z/aYMvv7Toj  
  
- precision is wrong  
- std::float128_t only gives link-error  
- on my machine 1. not even boost::float128_t(ype) - link-error  
  
I think this is due to the missing #define  
BOOST_CHARCONV_HAS_QUADMATH  
BOOST_CHARCONV_HAS_FLOAT128  
which causes the library to be compiled incorrectly.  
  
After (at least) 2 years of development, boost::charconv still does not work.  
I have never seen such a buggy boost library before.  
  
  
1. openSuse Tumbleweed, all packages are up to date

---

## Comment 8

> Username: mborland  
> Created at: 2024-10-18 13:20:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-2422464912  

> FP128 still does not work  
>   
> see https://godbolt.org/z/aYMvv7Toj  
>   
> * precision is wrong  
> * std::float128_t only gives link-error  
> * on my machine 1. not even boost::float128_t(ype) - link-error  
>   
> I think this is due to the missing #define BOOST_CHARCONV_HAS_QUADMATH BOOST_CHARCONV_HAS_FLOAT128 which causes the library to be compiled incorrectly.  
>  
  
I'll take a look. I have no idea how godbolt is building and linking the libraries so that's likely harder to work through.   
   
> After (at least) 2 years of development, boost::charconv still does not work. I have never seen such a buggy boost library before.  
>   
  
Nobody is forcing you to use this library, math, or any other boost library. I'm not sure why you feel the need to consistently include remarks like this on your issues. You can either a) gain some sense of decorum, or b) I can ignore your issues. Your choice.

---

## Comment 9

> Username: gpeterhoff  
> Created at: 2024-10-24 00:59:07 UTC  
> Url: https://github.com/boostorg/charconv/issues/166#issuecomment-2433987137  

It's not at all about wanting to make a personal name for myself.  
I want boost libraries to be error-free. This is also their self-imposed claim. After all, boost is used by many developers who assume/trust in error-free implementations.  
  
But unfortunately I don't see this with charconv. I had formulated some problems. They were (partly) fixed once, but were (partly) included again in the next version. I am referring in particular to buffer overflows.  
  
If you provide a library, you should also solve reported problems. I find your statement “then don't use it” unacceptable.  
Here are a few tests that should help.  
[test_charconv.cpp.txt](https://github.com/user-attachments/files/17499530/test_charconv.cpp.txt)
