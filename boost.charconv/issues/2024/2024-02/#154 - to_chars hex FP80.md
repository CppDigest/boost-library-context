# #154 - to_chars hex FP80 [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-02 18:31:06 UTC  
> Updated at: 2024-02-09 10:08:15 UTC  
> Closed at: 2024-02-09 10:08:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/154  

Test value: -35896.53987658756543653653365436f128  
  
FP80  
hex not work  
(std  , hex       )	-8.c388a355a1f783ap+12	(22, Success)  
(boost, hex       )	-1.0000000000000c388ap+81950	(28, Success)  
  
FP16  
scientific different precision  
(std  , scientific)	-3.59e+04	(9, Success)  
(boost, scientific)	-3.5904e+04	(11, Success)  
  
BF16  
scientific different precision  
(std  , scientific)	-3.59e+04	(9, Success)  
(boost, scientific)	-3.5904e+04	(11, Success)

---

## Comment 1

> Username: mborland  
> Created at: 2024-02-06 10:41:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1929236204  

In the FP16 case I think we are doing the correct thing and the STL is not since `max_digits10` for `_Float16` is 5. The BF16 case is wrong for both since `max_digits10` for the type is 4. See: https://godbolt.org/z/3sYWKE4s5 since they are not listed in cppref.

---

## Comment 2

> Username: mborland  
> Created at: 2024-02-06 10:53:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1929263704  

Moved issue for bfloat to https://github.com/cppalliance/charconv/issues/156

---

## Comment 3

> Username: jwakely  
> Created at: 2024-02-07 23:23:05 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933103969  

What do "std" and "STL" mean here? All the standard library implementations, or a particular one?  
  
How would I reproduce those results, and compare them to a given "std" impl?

---

## Comment 4

> Username: jwakely  
> Created at: 2024-02-08 00:03:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933140058  

> In the FP16 case I think we are doing the correct thing and the STL is not since `max_digits10` for `_Float16` is 5.   
  
Assuming that the test is calling `to_chars` for value `(float16_t)-35896.5398765` with `chars_format::scientific` and no precision argument, I think the correct answer is indeed `-3.59e+04`  
  
The standard says:  
  
>  The functions that take a floating-point value but not a precision parameter ensure that the string  
representation consists of the smallest number of characters such that there is at least one digit before the  
radix point (if present) and parsing the representation using the corresponding `from_chars` function recovers  
`value` exactly.  
  
The value as `float16_t` is rounded to `-35904.0` and the shortest rep that allows recovering the same value is `-3.59e+04` because both -35900 and -35904 will produce the same `float16_t` value (the next f16 value towards 0.0 is -35872).  
  
i.e. I think there's no need to print `max_digits10` digits if they don't affect the value that would be parsed back  and omitting them would be shorter.  
  
Of course this assumes the platform where the test was run really does convert `(float16_t)-35896.5398765` to `-35904.0` and uses the binary16 format, which can't represent anything between -35904 and -35872.

---

## Comment 5

> Username: mborland  
> Created at: 2024-02-08 07:09:21 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933473142  

> What do "std" and "STL" mean here? All the standard library implementations, or a particular one?  
>   
> How would I reproduce those results, and compare them to a given "std" impl?  
  
I was able to reproduce with Fedora 39 provided GCC 13.2.1 with the attached test set from the OP that was sent on the boost mailing list.  
  
[test_gero.cpp.txt](https://github.com/boostorg/charconv/files/14205422/test_gero.cpp.txt)

---

## Comment 6

> Username: mborland  
> Created at: 2024-02-08 07:18:06 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933486252  

> Assuming that the test is calling `to_chars` for value `(float16_t)-35896.5398765` with `chars_format::scientific` and no precision argument, I think the correct answer is indeed `-3.59e+04`  
>   
  
Based off your argument below I believe you are correct.  
  
> The standard says:  
>   
> > The functions that take a floating-point value but not a precision parameter ensure that the string  
> > representation consists of the smallest number of characters such that there is at least one digit before the  
> > radix point (if present) and parsing the representation using the corresponding `from_chars` function recovers  
> > `value` exactly.  
>   
> The value as `float16_t` is rounded to `-35904.0` and the shortest rep that allows recovering the same value is `-3.59e+04` because both -35900 and -35904 will produce the same `float16_t` value (the next f16 value towards 0.0 is -35872).  
>   
> i.e. I think there's no need to print `max_digits10` digits if they don't affect the value that would be parsed back and omitting them would be shorter.  
>   
> Of course this assumes the platform where the test was run really does convert `(float16_t)-35896.5398765` to `-35904.0` and uses the binary16 format, which can't represent anything between -35904 and -35872.  
  
This may be an ignorant question, but with GCC-13 adding excess precision support as the default aren't `float16_t`s being evaluated as 32-bit `floats` in the background? I am casting the 16-bit types to `float` and then using the `float` `to_chars` implementation.

---

## Comment 7

> Username: jwakely  
> Created at: 2024-02-08 08:03:07 UTC  
> Updated at: 2024-02-08 08:04:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933542877  

> > What do "std" and "STL" mean here? All the standard library implementations, or a particular one?  
> > How would I reproduce those results, and compare them to a given "std" impl?  
>   
> I was able to reproduce with Fedora 39 provided GCC 13.2.1 with the attached test set from the OP that was sent on the boost mailing list.  
  
Thanks, I'll check that out.  
  
> This may be an ignorant question,   
  
Not at all! Even @pppalka and I had to double-check what libstdc++ does when discussing this yesterday.  
  
>but with GCC-13 adding excess precision support as the default aren't float16_ts being evaluated as 32-bit floats in the background?  
  
For the overload of `to_chars` that takes a precision we cast `float16_t` to `float` and use the existing `float` overload. For the overload without a precision it's implemented for `float16_t` using the generic Ryu routines for shortest rep.

---

## Comment 8

> Username: mborland  
> Created at: 2024-02-08 08:10:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933556702  

>   
> For the overload of `to_chars` that takes a precision we cast `float16_t` to `float` and use the existing `float` overload. For the overload without a precision it's implemented for `float16_t` using the generic Ryu routines for shortest rep.  
  
That makes sense; I'll fix this with a similar approach. Thanks as always.

---

## Comment 9

> Username: jwakely  
> Created at: 2024-02-08 10:50:30 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933815098  

> > What do "std" and "STL" mean here? All the standard library implementations, or a particular one?  
> > How would I reproduce those results, and compare them to a given "std" impl?  
>   
> I was able to reproduce with Fedora 39 provided GCC 13.2.1 with the attached test set from the OP that was sent on the boost mailing list.  
  
I think I'm doing something wrong because I get linker errors for symbols that I expect to be in libboost_charconv but I'll figure that out later. If I comment out the uses of `boost::to_chars` and just use `std::to_chars` then I get:  
  
std::bfloat16_t  
(std  , scientific)     -3.58e+04       (9, Success)  
  
Which is what I got when I tried to reproduce the results, rather than -3.59e+04 as shown above:  
  
> BF16 scientific different precision  
> (std , scientific) -3.59e+04 (9, Success)  
> (boost, scientific) -3.5904e+04 (11, Success)  
  
@gpeterhoff  was that a copy & paste error?  
  
The value of `(bfloat16_t)-35896.5` is -35840.0 so `8` rather than `9` is the correct figure in the second decimal place.

---

## Comment 10

> Username: mborland  
> Created at: 2024-02-08 11:00:08 UTC  
> Url: https://github.com/boostorg/charconv/issues/154#issuecomment-1933831368  

This is what I get on current develop. Looks like a copy paste error:  
  
```  
_Float128  
(std  , scientific)	-3.589653987658756543653653365436e+04	(37, Success)  
(boost, scientific)	-3.589653987658756543653653365436e+04	(37, Success)  
  
(std  , fixed     )	-35896.53987658756543653653365436	(33, Success)  
(boost, fixed     )	-35896.53987658756543653653365436	(33, Success)  
  
(std  , hex       )	-1.1871146ab43ef0735e163704ac67p+15	(35, Success)  
(boost, hex       )	-1.1871146ab43ef0735e163704ac67p+15	(35, Success)  
  
(std  , general   )	-35896.53987658756543653653365436	(33, Success)  
(boost, general   )	-35896.53987658756543653653365436	(33, Success)  
  
  
long double  
(std  , scientific)	-3.5896539876587565438e+04	(26, Success)  
(boost, scientific)	-3.5896539876587565438e+04	(26, Success)  
  
(std  , fixed     )	-35896.539876587565438	(22, Success)  
(boost, fixed     )	-35896.539876587565438	(22, Success)  
  
(std  , hex       )	-8.c388a355a1f783ap+12	(22, Success)  
(boost, hex       )	-1.0000000000000c388ap+8470558	(30, Success)  
  
(std  , general   )	-35896.539876587565438	(22, Success)  
(boost, general   )	-35896.539876587565438	(22, Success)  
  
  
_Float64  
(std  , scientific)	-3.5896539876587565e+04	(23, Success)  
(boost, scientific)	-3.5896539876587565e+04	(23, Success)  
  
(std  , fixed     )	-35896.539876587565	(19, Success)  
(boost, fixed     )	-35896.539876587565	(19, Success)  
  
(std  , hex       )	-1.1871146ab43efp+15	(20, Success)  
(boost, hex       )	-1.1871146ab43efp+15	(20, Success)  
  
(std  , general   )	-35896.539876587565	(19, Success)  
(boost, general   )	-35896.539876587565	(19, Success)  
  
  
_Float32  
(std  , scientific)	-3.589654e+04	(13, Success)  
(boost, scientific)	-3.589654e+04	(13, Success)  
  
(std  , fixed     )	-35896.54	(9, Success)  
(boost, fixed     )	-35896.54	(9, Success)  
  
(std  , hex       )	-1.187114p+15	(13, Success)  
(boost, hex       )	-1.187114p+15	(13, Success)  
  
(std  , general   )	-35896.54	(9, Success)  
(boost, general   )	-35896.54	(9, Success)  
  
  
_Float16  
(std  , scientific)	-3.59e+04	(9, Success)  
(boost, scientific)	-3.5904e+04	(11, Success)  
  
(std  , fixed     )	-35904	(6, Success)  
(boost, fixed     )	-35904	(6, Success)  
  
(std  , hex       )	-1.188p+15	(10, Success)  
(boost, hex       )	-1.188p+15	(10, Success)  
  
(std  , general   )	-35904	(6, Success)  
(boost, general   )	-35904	(6, Success)  
  
  
std::bfloat16_t  
(std  , scientific)	-3.58e+04	(9, Success)  
(boost, scientific)	-35840	(6, Success)  
  
(std  , fixed     )	-35840	(6, Success)  
(boost, fixed     )	-35840	(6, Success)  
  
(std  , hex       )	-1.18p+15	(9, Success)  
(boost, hex       )	-1.180p+15	(10, Success)  
  
(std  , general   )	-35840	(6, Success)  
(boost, general   )	-35840	(6, Success)  
  
```
