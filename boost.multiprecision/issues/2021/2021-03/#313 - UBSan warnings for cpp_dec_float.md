# #313 - UBSan warnings for cpp_dec_float [Closed]

> Username: justusranvier  
> Created at: 2021-03-30 16:21:56 UTC  
> Updated at: 2021-05-11 18:41:22 UTC  
> Closed at: 2021-05-11 18:41:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313  

The following test program will generate ubsan diagnostics if compiled with -fsanitize=undefined,integer using clang 11.1 and boost 1.75.0:  
  
```c++  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
namespace mp = boost::multiprecision;  
  
auto main() -> int  
{  
    using Float = mp::cpp_dec_float_100;  
    auto test = mp::pow(Float{2}, 30) * mp::pow(Float{10}, -3);  
  
    return 0;  
}  
```  
  
  
```  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:554:51: runtime error: unsigned integer overflow: 0 - 67108864 cannot be represented in type 'unsigned int'  
    #0 0x258dad in bool boost::detail::lexical_ostream_limited_src<char, std::__1::char_traits<char> >::shr_signed<int>(int&) (/root/test/a.out+0x258dad)  
    #1 0x258962 in boost::detail::lexical_ostream_limited_src<char, std::__1::char_traits<char> >::operator>>(int&) (/root/test/a.out+0x258962)  
    #2 0x2585c7 in boost::detail::lexical_converter_impl<int, char const*>::try_convert(char const* const&, int&) (/root/test/a.out+0x2585c7)  
    #3 0x25847a in bool boost::conversion::detail::try_lexical_convert<int, char const*>(char const* const&, int&) (/root/test/a.out+0x25847a)  
    #4 0x25668a in int boost::lexical_cast<int, char const*>(char const* const&) (/root/test/a.out+0x25668a)  
    #5 0x253de3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::rd_string(char const*) (/root/test/a.out+0x253de3)  
    #6 0x253b8f in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=(char const*) (/root/test/a.out+0x253b8f)  
    #7 0x25219e in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float(char const*) (/root/test/a.out+0x25219e)  
    #8 0x24f5e4 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::min() (/root/test/a.out+0x24f5e4)  
    #9 0x24b89d in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::initializer::initializer() (/root/test/a.out+0x24b89d)  
    #10 0x24b840 in __cxx_global_var_init (/root/test/a.out+0x24b840)  
    #11 0x270bb4 in __libc_csu_init (/root/test/a.out+0x270bb4)  
    #12 0x7fbdca4d0dc9 in __libc_start_main (/lib64/libc.so.6+0x23dc9)  
    #13 0x2297d9 in _start (/root/test/a.out+0x2297d9)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:554:51 in  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2073:62: runtime error: unsigned integer overflow: 18446744073709551609 + 8 cannot be represented in type 'unsigned long'  
    #0 0x2550fe in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::rd_string(char const*) (/root/test/a.out+0x2550fe)  
    #1 0x253b8f in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=(char const*) (/root/test/a.out+0x253b8f)  
    #2 0x25219e in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float(char const*) (/root/test/a.out+0x25219e)  
    #3 0x269232 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::pow2(long long) (/root/test/a.out+0x269232)  
    #4 0x26f323 in boost::enable_if_c<boost::is_floating_point<long double>::value, boost::multiprecision::backends::cpp_dec_float<100u, int, void>&>::type boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=<long double>(long double) (/root/test/a.out+0x26f323)  
    #5 0x26ebf3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float<long double>(long double, boost::enable_if_c<is_floating_point<long double>::value, void>::type*) (/root/test/a.out+0x26ebf3)  
    #6 0x268dad in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::half() (/root/test/a.out+0x268dad)  
    #7 0x24b8d3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::initializer::initializer() (/root/test/a.out+0x24b8d3)  
    #8 0x24b840 in __cxx_global_var_init (/root/test/a.out+0x24b840)  
    #9 0x270bb4 in __libc_csu_init (/root/test/a.out+0x270bb4)  
    #10 0x7fbdca4d0dc9 in __libc_start_main (/lib64/libc.so.6+0x23dc9)  
    #11 0x2297d9 in _start (/root/test/a.out+0x2297d9)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/multiprecision/cpp_dec_float.hpp:2073:62 in  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2689:47: runtime error: implicit conversion from type 'boost::long_long_type' (aka 'long long') of value -30 (64-bit, signed) to type 'unsigned long long' changed the value to 18446744073709551586 (64-bit, unsigned)  
    #0 0x26e79d in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::pow2(long long) (/root/test/a.out+0x26e79d)  
    #1 0x26f4ed in boost::enable_if_c<boost::is_floating_point<long double>::value, boost::multiprecision::backends::cpp_dec_float<100u, int, void>&>::type boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=<long double>(long double) (/root/test/a.out+0x26f4ed)  
    #2 0x26ebf3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float<long double>(long double, boost::enable_if_c<is_floating_point<long double>::value, void>::type*) (/root/test/a.out+0x26ebf3)  
    #3 0x268dad in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::half() (/root/test/a.out+0x268dad)  
    #4 0x24b8d3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::initializer::initializer() (/root/test/a.out+0x24b8d3)  
    #5 0x24b840 in __cxx_global_var_init (/root/test/a.out+0x24b840)  
    #6 0x270bb4 in __libc_csu_init (/root/test/a.out+0x270bb4)  
    #7 0x7fbdca4d0dc9 in __libc_start_main (/lib64/libc.so.6+0x23dc9)  
    #8 0x2297d9 in _start (/root/test/a.out+0x2297d9)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/multiprecision/cpp_dec_float.hpp:2689:47 in  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2689:49: runtime error: unsigned integer overflow: 18446744073709551586 + 127 cannot be represented in type 'unsigned long long'  
    #0 0x26e866 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::pow2(long long) (/root/test/a.out+0x26e866)  
    #1 0x26f4ed in boost::enable_if_c<boost::is_floating_point<long double>::value, boost::multiprecision::backends::cpp_dec_float<100u, int, void>&>::type boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=<long double>(long double) (/root/test/a.out+0x26f4ed)  
    #2 0x26ebf3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float<long double>(long double, boost::enable_if_c<is_floating_point<long double>::value, void>::type*) (/root/test/a.out+0x26ebf3)  
    #3 0x268dad in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::half() (/root/test/a.out+0x268dad)  
    #4 0x24b8d3 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::initializer::initializer() (/root/test/a.out+0x24b8d3)  
    #5 0x24b840 in __cxx_global_var_init (/root/test/a.out+0x24b840)  
    #6 0x270bb4 in __libc_csu_init (/root/test/a.out+0x270bb4)  
    #7 0x7fbdca4d0dc9 in __libc_start_main (/lib64/libc.so.6+0x23dc9)  
    #8 0x2297d9 in _start (/root/test/a.out+0x2297d9)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/multiprecision/cpp_dec_float.hpp:2689:49 in  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-03-30 20:11:59 UTC  
> Updated at: 2021-03-30 20:12:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-810546137  

> The following test program will generate ubsan diagnostics if compiled with -fsanitize=undefined,integer using clang 11.1 and boost 1.75.0  
  
Thank you for raising this issue. I believe there is a 64-bit siged integer overflow. I detected something similar in the hostorical work of this project recently.  
  
So I think I can figure out what is wrong and how to fix it.  
  
I will get on this issue, which I believe is an actual bug or defect.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2021-03-30 21:31:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-810591446  

I think there are several independent problems, all of which seem to be happening in the so-called _static initialization_ which runs before the call to `main`. At this point in the program, we are initializing certain values needed by the multiple-precision type that should be initialized before entering `main`. This code is purposely hidden from the operations in `main`.  
  
On line 2073 I can see a potential cause in the signed-ness of the variable `n_shift` (which maybe would be better as `ptrdiff_t`).  
On line 2689, the value of the exponent after adding the size of an array seems like it might  overflow.  
  
The first issue with `lexical_cast` I can not really identify yet. I will look for fix(es).

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-03-31 07:52:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-810857623  

@ckormanyos : the first error is an issue with lexical_cast, I've just filed a bug report on it here: https://github.com/boostorg/lexical_cast/issues/45

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-03-31 08:08:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-810867924  

@ckormanyos : The issue can be reduced to:  
  
```  
   boost::multiprecision::backends::cpp_dec_float<100>::half();  
```  
And at cpp_dec_float.hpp:1995 if exp is negative we change the value by assigning to a size_t, this then triggers the subsequent error.  It seems to all work, but I'm not convinced it's quite correct.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-03-31 08:47:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-810893784  

> And at cpp_dec_float.hpp:1995 if exp is negative we change the value by assigning to a size_t, this then triggers the subsequent error.  
  
Yes @jzmaddock, this is either line 1995 in 1.76 or line 2068 1.75 in `cpp_dec_float.hpp`.  
  
In my historical revisions of the project (and I do not know exactly when or why I changed it in these derived projects), I use signed `ptrdiff_t` for the exponent modulus result (instead of `size_t`). See JPG pic below. Using the signed type would make more sense to me. I will try out the signed type and see if stuff works in CI, local tests, etc.  
  
![ptrdiff_t](https://user-images.githubusercontent.com/2404721/113117208-81cfaa80-920e-11eb-9595-fb6ac4064e3f.JPG)

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-04-03 21:21:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-812927241  

Fixed by #314 (in the Multiprecision part)

---

## Comment 7

> Username: shadymohamedamin  
> Created at: 2021-04-04 11:13:44 UTC  
> Updated at: 2021-04-04 11:17:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-813015189  

i want the email of engineer Christopher Kormanyos @ckormanyos

---

## Comment 8

> Username: shadymohamedamin  
> Created at: 2021-04-04 19:38:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-813088476  

@ckormanyos

---

## Comment 9

> Username: ckormanyos  
> Created at: 2021-04-04 20:43:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-813096532  

> engineer Christopher   
  
You can almost always reach me at the Boost developer board.

---

## Comment 10

> Username: shadymohamedamin  
> Created at: 2021-04-04 20:57:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-813098058  

@ckormanyos  where is the link of the boost developer board

---

## Comment 11

> Username: ckormanyos  
> Created at: 2021-05-11 18:41:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/313#issuecomment-838978491  

The warnings we are responsible for haave been handled in #323
