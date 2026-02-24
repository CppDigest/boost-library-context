# #686 - Array access warnings from cpp_bin_float [Closed]

> Username: jzmaddock  
> Created at: 2025-05-01 10:26:17 UTC  
> Updated at: 2025-06-29 09:01:36 UTC  
> Closed at: 2025-06-29 09:01:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686  

When building test_gamma_mp in Boost.Math with GCC-13 Mingw:  
  
```  
In file included from ..\..\../boost/multiprecision/cpp_int.hpp:2354,  
                 from ..\..\../boost/multiprecision/cpp_bin_float.hpp:15,  
                 from test_gamma_mp.cpp:19:  
In function 'constexpr void boost::multiprecision::backends::right_shift_generic(Int&, boost::multiprecision::double_limb_type) [with Int = cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>]',  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_right_shift(cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 151; long long unsigned int MaxBits1 = 151; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = void]' at ..\..\../boost/multiprecision/cpp_int/bitwise.hpp:643:26,  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_right_shift(cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 151; long long unsigned int MaxBits1 = 151; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = void]' at ..\..\../boost/multiprecision/cpp_int/bitwise.hpp:604:1,  
    inlined from 'void boost::multiprecision::backends::copy_and_round(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, Int&, std::ptrdiff_t) [with unsigned int Digits = 453; digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinE = 0; Exponent MaxE = 0; Int = cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>]' at ..\..\../boost/multiprecision/cpp_bin_float.hpp:726:32,  
    inlined from 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>& boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::operator=(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&) [with unsigned int D = 45; boost::multiprecision::backends::digit_base_type B = boost::multiprecision::backends::digit_base_10; A = void; E = int; E MinE = 0; E MaxE = 0; unsigned int Digits = 453; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]' at ..\..\../boost/multiprecision/cpp_bin_float.hpp:279:24:  
..\..\../boost/multiprecision/cpp_int/bitwise.hpp:598:14: warning: array subscript [7, 1152921504606846975] is outside array bounds of 'boost::multiprecision::backends::cpp_bin_float<45>::rep_type [1]' {aka 'boost::multiprecision::backends::cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> [1]'} [-Warray-bounds=]  
  598 |    pr[i] = pr[i + offset] >> shift;  
      |            ~~^  
..\..\../boost/multiprecision/cpp_bin_float.hpp: In function 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>& boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::operator=(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&) [with unsigned int D = 45; boost::multiprecision::backends::digit_base_type B = boost::multiprecision::backends::digit_base_10; A = void; E = int; E MinE = 0; E MaxE = 0; unsigned int Digits = 453; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]':  
..\..\../boost/multiprecision/cpp_bin_float.hpp:276:67: note: at offset 56 into object 'b' of size 48  
  276 |          typename cpp_bin_float<D, B, A, E, MinE, MaxE>::rep_type b(f.bits());  
      |                                                                   ^  
In function 'constexpr void boost::multiprecision::backends::right_shift_generic(Int&, boost::multiprecision::double_limb_type) [with Int = cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>]',  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_right_shift(cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 151; long long unsigned int MaxBits1 = 151; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = void]' at ..\..\../boost/multiprecision/cpp_int/bitwise.hpp:643:26,  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_right_shift(cpp_int_backend<MinBits, MaxBits, boost::multiprecision::unsigned_magnitude, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 151; long long unsigned int MaxBits1 = 151; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = void]' at ..\..\../boost/multiprecision/cpp_int/bitwise.hpp:604:1,  
    inlined from 'void boost::multiprecision::backends::copy_and_round(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, Int&, std::ptrdiff_t) [with unsigned int Digits = 453; digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinE = 0; Exponent MaxE = 0; Int = cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>]' at ..\..\../boost/multiprecision/cpp_bin_float.hpp:726:32,  
    inlined from 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>& boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::operator=(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&) [with unsigned int D = 45; boost::multiprecision::backends::digit_base_type B = boost::multiprecision::backends::digit_base_10; A = void; E = int; E MinE = 0; E MaxE = 0; unsigned int Digits = 453; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]' at ..\..\../boost/multiprecision/cpp_bin_float.hpp:279:24:  
..\..\../boost/multiprecision/cpp_int/bitwise.hpp:598:10: warning: array subscript [7, 1152921504606846975] is outside array bounds of 'boost::multiprecision::backends::cpp_bin_float<45>::rep_type [1]' {aka 'boost::multiprecision::backends::cpp_int_backend<151, 151, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> [1]'} [-Warray-bounds=]  
  598 |    pr[i] = pr[i + offset] >> shift;  
      |    ~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\../boost/multiprecision/cpp_bin_float.hpp: In function 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>& boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::operator=(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&) [with unsigned int D = 45; boost::multiprecision::backends::digit_base_type B = boost::multiprecision::backends::digit_base_10; A = void; E = int; E MinE = 0; E MaxE = 0; unsigned int Digits = 453; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_2; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]':  
..\..\../boost/multiprecision/cpp_bin_float.hpp:276:67: note: at offset 56 into object 'b' of size 48  
  276 |          typename cpp_bin_float<D, B, A, E, MinE, MaxE>::rep_type b(f.bits());  
      |                                                                   ^  
```  
  
Not investigated or understood at present.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-05-05 19:59:27 UTC  
> Updated at: 2025-05-05 19:59:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-2852192557  

I've seen a bunch of these ever since GCC 12 came on the scene. Sometimes, I can prove they are bogus. Sometimes, I can't distinguish between bogus/real.  
  
It is then that we look more intently into ASAN runs and ensure that they exercise these ranges. That can, however, be very difficult to do reliably. To this date, I've not ever actually had a non-bogus one of these. But that may be my subjective/wishful thinking.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-06-27 21:18:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-3014374869  

John, I came to the conclusion, .. they are bogus. But I can't really _prove_ this.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-06-28 16:03:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-3015608480  

I think I can:  
  
It's complaining that array access in:  
  
```  
   pr[i] = pr[i + offset] >> shift;  
```  
  
Is potentially out of range, but we know that `pr` is an array of size `ors` and that the loop prior to this line terminates when `i + offset + 1 < ors`.  Which means either:  
  
* The loop did not execute at all: in that case, `i == 0`, so we just need `offset < ors` which is true because we filter the alternative off right at the start.  
* Or the loop terminated with a final increment on i so we have `i + offset + 1 == ors` or if you prefer `i + offset < ors` which is what we need.  
  
I wonder if an `assume` attribute would silence the warning?  I'll have a look...

---

## Comment 4

> Username: mborland  
> Created at: 2025-06-28 16:06:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-3015617557  

>   
> I wonder if an `assume` attribute would silence the warning? I'll have a look...  
  
Assuming things may cause additional warnings with Clang: https://clang.llvm.org/docs/DiagnosticsReference.html#wassume

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-06-28 18:04:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-3015962963  

`assume` doesn't work anyway.  Also only triggered when building with -O3 for some reason.  Will use a pragma which does work.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2025-06-28 18:19:52 UTC  
> Updated at: 2025-06-28 18:29:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/686#issuecomment-3015969644  

On a side-note (and this is subjective) when GCC 12 came about I started to get all kinds of warnings related to array-access bounds and similar others. I studied them for hours. I ran ASAN and TSAN and UBSAN checks and fuzzing checks, but none of these warnings was ever confirmed to actually break array-bounds. I never confirmed a single one of these warnings.  
  
In unrelated projects I have all kinds of `#pragma`s to disable such warnings.  
  
And sadly this has turned dangerous, since I would like to deal properly with warnings, but only if they are based on merit and not bogus.  
  
Things seem to be getting better. But I think there are/were problems for a few GCC versions regarding array bounds.  
  
Of course, and again, this is highly subjective.
