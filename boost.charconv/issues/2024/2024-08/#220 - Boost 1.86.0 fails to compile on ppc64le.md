# #220 - Boost 1.86.0 fails to compile on ppc64le [Closed]

> Username: iv-m  
> Created at: 2024-08-15 12:05:19 UTC  
> Updated at: 2024-08-27 13:52:56 UTC  
> Closed at: 2024-08-27 13:52:55 UTC  
> Url: https://github.com/boostorg/charconv/issues/220  

When trying to compile Boost 1.86.0 on my ppc64le machine, I get the following error:  
  
```  
  
    "g++"  -fvisibility-inlines-hidden -fPIC -m64 -pthread -fvisibility=hidden -pipe -frecord-gcc-switches -Wall -g -O2 -flto=auto -ffat-lto-objects -fno-strict-aliasing  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHARCONV_DYN_LINK=1 -DBOOST_CHARCONV_HAS_QUADMATH -DBOOST_CHARCONV_SOURCE=1 -DBOOST_COBALT_USE_STD_PMR=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/charconv/build/gcc-13/release/power_64/optimization-off/pch-off/threading-multi/visibility-hidden/to_chars.o" "libs/charconv/build/../src/to_chars.cpp"  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
libs/charconv/build/../src/to_chars_float_impl.hpp: In function 'boost::charconv::to_chars_result boost::charconv::detail::to_chars_hex(char*, char*, Real, int)':  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:140: error: 'ieee754_binary80' was not declared in this scope; did you mean 'ieee754_binary64'?  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                            ^~~~~~~~~~~~~~~~  
      |                                                                                                                                            ieee754_binary64  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:156: error: template argument 3 is invalid  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                                            ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:277:157: error: '<declaration error>' is not a template [-fpermissive]  
  277 |                     typename std::conditional<std::is_same<Real, __float128>::value || BOOST_CHARCONV_LDBL_BITS == 128, ieee754_binary128, ieee754_binary80>::type  
      |                                                                                                                                                             ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:13: error: template argument 3 is invalid  
  285 |             >::type>::type  
      |             ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:14: error: '<declaration error>' is not a template [-fpermissive]  
  285 |             >::type>::type  
      |              ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:20: error: template argument 3 is invalid  
  285 |             >::type>::type  
      |                    ^  
libs/charconv/build/../src/to_chars_float_impl.hpp:285:21: error: '<declaration error>' is not a template [-fpermissive]  
  285 |             >::type>::type  
      |                     ^~  
libs/charconv/build/../src/to_chars_float_impl.hpp:302:67: error: 'type_layout' has not been declared  
  302 |     const Unsigned_Integer denorm_mask = (Unsigned_Integer(1) << (type_layout::significand_bits)) - 1;  
      |                                                                   ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:304:61: error: 'type_layout' has not been declared  
  304 |     std::uint64_t exponent = extract_exp(value, uint_value, type_layout::significand_bits);  
      |                                                             ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:365:33: error: 'type_layout' has not been declared  
  365 |         unbiased_exponent = 1 + type_layout::exponent_bias;  
      |                                 ^~~~~~~~~~~  
libs/charconv/build/../src/to_chars_float_impl.hpp:370:40: error: 'type_layout' has not been declared  
  370 |         unbiased_exponent = exponent + type_layout::exponent_bias;  
      |                                        ^~~~~~~~~~~  
```

---

## Comment 1

> Username: iv-m  
> Created at: 2024-08-15 12:12:39 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2291158698  

It looks like the code in `to_chars_hex` assumes that if the machine has `float128`, it also has a supported `long double` layout. But this machine has older weird long double layout that is not supported by Boost.Charconv.

---

## Comment 2

> Username: mborland  
> Created at: 2024-08-15 12:21:09 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2291169376  

Isn't this the same as: https://github.com/boostorg/charconv/issues/182#issuecomment-2069075260? Does it still build with GNU extensions turned off? I was never able to replicate that issue. After your issues with last release I added PPC64LE to the CI battery: https://github.com/boostorg/charconv/blob/develop/.github/workflows/qemu.yml#L43, and everything seems green. Are you able to send a patch for your specific platform?

---

## Comment 3

> Username: iv-m  
> Created at: 2024-08-15 12:53:21 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2291213310  

> Isn't this the same as: https://github.com/boostorg/charconv/issues/182#issuecomment-2069075260?  
  
Well, I totally forgot about that comment. Yes, it looks like it's the same issue.  
  
> Are you able to send a patch for your specific platform?  
  
Sure. I have a quck patch. I'll send a PR after some cleanup and more testing.

---

## Comment 4

> Username: mborland  
> Created at: 2024-08-15 13:00:03 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2291223864  

> Sure. I have a quck patch. I'll send a PR after some cleanup and more testing.  
  
Thank you

---

## Comment 5

> Username: fedapo  
> Created at: 2024-08-23 21:06:58 UTC  
> Updated at: 2024-08-24 06:48:30 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2307811594  

FTR, I am having the same issue with the `ppc64le` architecture when using `gcc 11`.  
  
As for the fix, isn't this just a matter of putting the definition of struct `ieee754_binary80` outside of a conditionally compiled block that is excluded from builds under the `ppc64le` architecture?  
Struct `ieee754_binary80` is not actually used in this case, but the code needs it regardless as it's parts of an `std::conditional`, hence the first error message.  
  
> I added PPC64LE to the CI battery [...] and everything seems green  
  
@mborland  I understand that some SFINEA mechanism might allow an undeclared/undefined symbol to be present in the "unused" part of `std::conditional`, but it seems this is not happening with gcc-11.  
Perhaps the CI is using a different gcc version?

---

## Comment 6

> Username: mborland  
> Created at: 2024-08-26 15:08:05 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2310445955  

> FTR, I am having the same issue with the `ppc64le` architecture when using `gcc 11`.  
>   
> As for the fix, isn't this just a matter of putting the definition of struct `ieee754_binary80` outside of a conditionally compiled block that is excluded from builds under the `ppc64le` architecture? Struct `ieee754_binary80` is not actually used in this case, but the code needs it regardless as it's parts of an `std::conditional`, hence the first error message.  
>   
> > I added PPC64LE to the CI battery [...] and everything seems green  
>   
> @mborland I understand that some SFINEA mechanism might allow an undeclared/undefined symbol to be present in the "unused" part of `std::conditional`, but it seems this is not happening with gcc-11. Perhaps the CI is using a different gcc version?  
  
The CI is running gcc-13 and clang-17 on QEMU PPC64LE since we have no native machines to test on, and it's cost prohibitive. Can you please try the patch: https://github.com/boostorg/charconv/commit/8796af49a050edc51e29a4a0bd487e4f79d13854.patch from the linked PR, and let me know if it works? The CI PPC64LE run is still showing green with the change.

---

## Comment 7

> Username: iv-m  
> Created at: 2024-08-27 08:48:25 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2311931192  

> Can you please try the patch: https://github.com/boostorg/charconv/commit/8796af49a050edc51e29a4a0bd487e4f79d13854.patch from the linked PR, and let me know if it works? The CI PPC64LE run is still showing green with the change.  
  
The patch looks fine and should work. Initially I came up with something like that, only I used `BOOST_CHARCONV_UNSUPPORTED_LONG_DOUBLE`, not the platform, as the condition. Unfortunately, I can give it a real try only later this week.  
  
Meanwhile, I pushed the patch I was working on as #222. For me, it fixes build on ppc64le machine, and does not break anything on x86_64 and aarch64 machines. Can you take a look? I hope what I'm doing there makes sense in general.

---

## Comment 8

> Username: fedapo  
> Created at: 2024-08-27 09:24:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2312007814  

> Can you please try the patch: https://github.com/boostorg/charconv/commit/8796af49a050edc51e29a4a0bd487e4f79d13854.patch from the linked PR, and let me know if it works? The CI PPC64LE run is still showing green with the change.  
  
I agree with @iv-m that it looks like it should work and I myself need some time to test, I only have access to a machine in the cloud and it'll take me a while to apply the patch there.  
  
I'd like to point out that the fixes I've seen all rely on using conditional compilation, which I think can be avoided. Keep in mind that the code works with other versions of gcc and I think this is because the code is correct, but relies on a SFINEA mechanism that is probably not fully implemented in the gcc-11 that we find for `ppc64le`. The type `ieee754_binary80` is never used for such architecture, replacing it with `ieee754_binary64` using preprocessor logic is not really needed as it will never be used in any case. I would suggest to just move the definition of `ieee754_binary80` close to the other `ieee754_*` structs, **_outside of any conditionally compiled block_**, so that all versions of gcc will be happy and no preprocess gymmicks are needed.  
This is only a suggestion, I am not the maintainer of this code and I'll be ok with any changes that fixes the issue.

---

## Comment 9

> Username: iv-m  
> Created at: 2024-08-27 10:47:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2312183961  

@fedapo, the error message in the head of this issue I've got when compiling boost 1.86.0 with gcc 13.2.1. So it's not just gcc-11 issue.  
  
I also don't see how SFINAE would work here. The constructions like  
  
```  
std::conditional<true, int, NON_EXISTENT_TYPE>::type  
```  
  
don't compile (e.g. https://godbolt.org/z/369cbqYTG), and I don't see such compilation errors can be avoided without the preprocessor.

---

## Comment 10

> Username: fedapo  
> Created at: 2024-08-27 11:08:13 UTC  
> Url: https://github.com/boostorg/charconv/issues/220#issuecomment-2312245717  

> I also don't see how SFINAE would work here. [...] I don't see such compilation errors can be avoided without the preprocessor.  
  
@iv-m I have not tried it, so I'm keen to believe you. My hypothesis was based on the fact that the type `ieee754_binary80` should not be defined for other toolchains that still run on `ppc64le`, yet without error according to what I read. In this case, I'm fine with the preprocessor, of course; it was never a strong objection in any case.  
Apologies for lengthening the discussion without need.
