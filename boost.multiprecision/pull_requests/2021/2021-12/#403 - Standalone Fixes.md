# #403 Standalone Fixes [Merged]

> Username: mborland  
> Created at: 2021-12-11 14:12:02 UTC  
> Updated at: 2022-01-02 12:24:54 UTC  
> Merged at: 2021-12-21 18:29:07 UTC  
> Closed at: 2021-12-21 18:29:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403  

Moves the discussion from the now merged #386

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-12-11 16:59:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-991711805  

Hi @mborland thanks for creating this PR for further progress.  
  
In our previous thread, you made a comment regarding using a multiprecision type for `__int128`. I think this could be potentially problematic (although I'm not sure). Problems may arise because the particular type `unsigned` `__int128` is being actually needed as `cpp_int`'s class-internal double-limb-type.  
  
So you might end up using a class that has not even yet been synthesized in order to build up class-internals?  
  
I could be wrong on this one though since i'm not exactly sure how the intent is that you had mentioned.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-12-11 17:20:45 UTC  
> Updated_at: 2021-12-11 17:21:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-991718827  

Hi @mborland and @jzmaddock   
  
The `noexcept` culprit seems to have been found. Thanks John!  
  
From our [previous chat](https://github.com/boostorg/multiprecision/pull/386#issuecomment-991601530), @jzmaddock mentions...  
  
> perhaps you could test making that method noexcept only when BOOST_NO_CXX17_IF_CONSTEXPR is not defined?  
  
The modification around line 1272 in `cpp_int.hpp` fixes the `noexcept` warnings [here](https://github.com/boostorg/multiprecision/commit/501406a0d38df174784034a7c900a2e5ef6ce9b0).  
  
There are a handful of other little changes in my branch for warning fixes on conversion and sign conversion (these are optional). My CI in the unrelated project ran green.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-12-11 17:28:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-991721015  

@ckormanyos if you look at the latest commit `boost::multiprecision::int128_type` is just an alias to `__int128`. Let me know if it suppresses the warnings for you like it should.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-12-12 08:08:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-991852418  

> look at the latest commit boost::multiprecision::int128_type is just an alias to __int128. Let me know if it suppresses the warnings for you like it should  
  
Hi @mborland. Thank you.  
  
Your recent commits eliminate almost all the warnings. For lack of a better communication method, I simply included full warnings under subjection to -`Wall -Wextra -pedantic` below.  
  
The two cases are:  
- develop branch  
- your standalone branch.  
  
Almost all warnings are gone from your branch with a couple remaining, that I did not, yet at the moment, analyze.   
  
**DEVELOP BRANCH**  
  
```C  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$ g++ -Wall -Wextra -pedantic -Os -march=native -std=c++11 -I/mnt/c/boost/modular_boost/boost/libs/multiprecision/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
In file included from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/number_base.hpp:18,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/is_variable_precision.hpp:9,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/precision.hpp:9,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp:11,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:31:18: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   31 | struct is_signed<__int128> : public std::integral_constant<bool, true> {};  
      |                  ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:33:27: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   33 | struct is_signed<unsigned __int128> : public std::integral_constant<bool, false> {};  
      |                           ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:35:20: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   35 | struct is_unsigned<__int128> : public std::integral_constant<bool, false> {};  
      |                    ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:37:29: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   37 | struct is_unsigned<unsigned __int128> : public std::integral_constant<bool, true> {};  
      |                             ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:39:20: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   39 | struct is_integral<__int128> : public std::integral_constant<bool, true> {};  
      |                    ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:41:29: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   41 | struct is_integral<unsigned __int128> : public std::integral_constant<bool, true> {};  
      |                             ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:43:22: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   43 | struct is_arithmetic<__int128> : public std::integral_constant<bool, true> {};  
      |                      ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:45:31: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   45 | struct is_arithmetic<unsigned __int128> : public std::integral_constant<bool, true> {};  
      |                               ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:47:22: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   47 | struct make_unsigned<__int128>  
      |                      ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:49:26: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   49 |    using type = unsigned __int128;  
      |                          ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:52:31: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   52 | struct make_unsigned<unsigned __int128>  
      |                               ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:54:26: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   54 |    using type = unsigned __int128;  
      |                          ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:57:20: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   57 | struct make_signed<__int128>  
      |                    ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:59:17: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   59 |    using type = __int128;  
      |                 ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:62:29: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   62 | struct make_signed<unsigned __int128>  
      |                             ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/traits/std_integer_traits.hpp:64:17: warning:  
ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   64 |    using type = __int128;  
      |                 ^~~~~~~~  
In file included from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp:12,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/default_ops.hpp: In function ‘void boost::multiprecision::default_ops::eval_karatsuba_sqrt(Backend&, const Backend&, Backend&, Backend&, size_t)’:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:1691:25: warning: ISO C++ does not support ‘__int128’ for ‘a’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                         ^  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:1691:30: warning: ISO C++ does not support ‘__int128’ for ‘b’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                              ^  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/default_ops.hpp:1691:35: warning: ISO C++ does not support ‘__int128’ for ‘c’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                                   ^  
In file included from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/number.hpp:16,  
                 from /mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp: At global scope:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp:23:49: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   23 | std::size_t hash_value(const unsigned __int128& val);  
      |                                                 ^~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp:25:47: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   25 | inline std::size_t hash_value(const __int128& val)  
      |                                               ^~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp: In function ‘std::size_t boost::multiprecision::detail::hash_value(const __int128&)’:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp:27:43: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   27 |    return hash_value(static_cast<unsigned __int128>(val));  
      |                                           ^~~~~~~~  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp: At global scope:  
/mnt/c/boost/modular_boost/boost/libs/multiprecision/include/boost/multiprecision/detail/hash.hpp:44:56: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   44 | inline std::size_t hash_value(const unsigned __int128& val)  
      |                                                        ^~~  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$  
```  
  
**STANDALONE BRANCH**  
  
```C  
chris@DESKTOP-Q6SS99B:~$ cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$ g++ -Wall -Wextra -pedantic -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/number.hpp:12,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp: In function ‘void boost::multiprecision::default_ops::eval_karatsuba_sqrt(Backend&, const Backend&, Backend&, Backend&, size_t)’:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:1691:25: warning: ISO C++ does not support ‘__int128’ for ‘a’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                         ^  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:1691:30: warning: ISO C++ does not support ‘__int128’ for ‘b’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                              ^  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:1691:35: warning: ISO C++ does not support ‘__int128’ for ‘c’ [-Wpedantic]  
 1691 |       unsigned __int128 a{}, b{}, c{};  
      |                                   ^  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/number.hpp:16,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp: At global scope:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp:23:49: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   23 | std::size_t hash_value(const unsigned __int128& val);  
      |                                                 ^~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp:25:47: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   25 | inline std::size_t hash_value(const __int128& val)  
      |                                               ^~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp: In function ‘std::size_t boost::multiprecision::detail::hash_value(const __int128&)’:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp:27:43: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   27 |    return hash_value(static_cast<unsigned __int128>(val));  
      |                                           ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp: At global scope:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/hash.hpp:44:56: warning: ISO C++ does not support ‘__int128’ for ‘val’ [-Wpedantic]  
   44 | inline std::size_t hash_value(const unsigned __int128& val)  
      |                                                        ^~~  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$  
```

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-12-12 11:28:27 UTC  
> Updated_at: 2021-12-12 11:30:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-991880409  

Thnks @mborland.  
  
Sorry Matt, I missed a few `__int128` warnings on `-pedantic`. My last report was for `cpp_dec_float`.  
  
I have found the following shown below for `cpp_bin_float`. Note that this should cover now dec-float, bin-float and `cpp_int` as well. (I'll run `gmp_float` and `mpfr_float`through ASAP.)  
  
**STANDALONE BRANCH** with `cpp_bin_float`  
  
```C  
chris@DESKTOP-Q6SS99B:~$ cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$ g++ -Wall -Wextra -pedantic -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
chris@DESKTOP-Q6SS99B:/mnt/c/Users/User/Documents/Ks/PC_Software/Test$ g++ -Wall -Wextra -pedantic -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
./test.cpp:10:1: warning: multi-line comment [-Wcomment]  
   10 | //TGT_INCLUDES  = -IC:/boost/modular_boost/boost/libs/multiprecision/include        \  
      | ^  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/endian.hpp:9,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int.hpp:14,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_bin_float.hpp:11,  
                 from ./test.cpp:60:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp: In function ‘constexpr T boost::multiprecision::backends::detail::type_max()’:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:25: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                         ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:29:65: note: in expansion of macro ‘INT128_MAX’  
   29 |    std::is_same<T, boost::multiprecision::int128_type>::value ? INT128_MAX :  
      |                                                                 ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:47: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                                               ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:29:65: note: in expansion of macro ‘INT128_MAX’  
   29 |    std::is_same<T, boost::multiprecision::int128_type>::value ? INT128_MAX :  
      |                                                                 ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:88:41: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   88 | #    define UINT128_MAX ((2 * (unsigned __int128) INT128_MAX) + 1)  
      |                                         ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:30:66: note: in expansion of macro ‘UINT128_MAX’  
   30 |    std::is_same<T, boost::multiprecision::uint128_type>::value ? UINT128_MAX :  
      |                                                                  ^~~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:25: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                         ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:88:51: note: in expansion of macro ‘INT128_MAX’  
   88 | #    define UINT128_MAX ((2 * (unsigned __int128) INT128_MAX) + 1)  
      |                                                   ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:30:66: note: in expansion of macro ‘UINT128_MAX’  
   30 |    std::is_same<T, boost::multiprecision::uint128_type>::value ? UINT128_MAX :  
      |                                                                  ^~~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:47: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                                               ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:88:51: note: in expansion of macro ‘INT128_MAX’  
   88 | #    define UINT128_MAX ((2 * (unsigned __int128) INT128_MAX) + 1)  
      |                                                   ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:30:66: note: in expansion of macro ‘UINT128_MAX’  
   30 |    std::is_same<T, boost::multiprecision::uint128_type>::value ? UINT128_MAX :  
      |                                                                  ^~~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp: In function ‘constexpr T boost::multiprecision::backends::detail::type_min()’:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:25: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                         ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:85:26: note: in expansion of macro ‘INT128_MAX’  
   85 | #    define INT128_MIN (-INT128_MAX - 1)  
      |                          ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:40:65: note: in expansion of macro ‘INT128_MIN’  
   40 |    std::is_same<T, boost::multiprecision::int128_type>::value ? INT128_MIN :  
      |                                                                 ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:82:47: warning: ISO C++ does not support ‘__int128’ for ‘type name’ [-Wpedantic]  
   82 | #    define INT128_MAX (__int128) (((unsigned __int128) 1 << ((__SIZEOF_INT128__ * __CHAR_BIT__) - 1)) - 1)  
      |                                               ^~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/standalone_config.hpp:85:26: note: in expansion of macro ‘INT128_MAX’  
   85 | #    define INT128_MIN (-INT128_MAX - 1)  
      |                          ^~~~~~~~~~  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_int/checked.hpp:40:65: note: in expansion of macro ‘INT128_MIN’  
   40 |    std::is_same<T, boost::multiprecision::int128_type>::value ? INT128_MIN :  
      |                                                                 ^~~~~~~~~~  
```

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-12-13 08:05:27 UTC  
> Updated_at: 2021-12-13 08:07:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-992204111  

Hi folks, this is moving along well.  
  
There are several issues I'd like to highlight and get feedback on.  
  
1. Are we testing standalone? I remember some comment(s) from @jzmaddock but did not understand the exact depth or meaning thereof. So @mborland do you think we have explicit standalone tests in the branch and/or on develop?  
2. When embedding `cpp_dec_float` versus `cpp_bin_float`, I find that bin-float adds about 200 additional kilobytes of program code almost entirely needed for string-streaming (via `std::stringstream`). Since my to total benchmark size (101 decimal digit cube root) is less than 100 kilobyte program code, adding 200 kilobyte for string-streaming is exorbitant. The heavy weight comes from reading string  limbs and converting them to `cpp_int`. I'd suggest we try to replace `std::stringstream`-ing with lightweight `std::stoul()` or similar as we did in `cpp_dec_float`.  
3. Now that standalone can basically, ... stand alone, it would be really helpful to ramp up the warning level. i have good experiences with adding GCC's `-Wconversion` and `-Wsign-conversion`. This results in about 25 warnings in dec-float and fewer than 50 warnings in bin-float. I would like to pursue resolution of these warnings since both i as well as a couple folks I know use such warnings and perpetually need to disable them for Boost. Are we agreed should I repair them? Or should I let them go?  
4. I will finish searching for `-pedantic` ISO-C++ non-conformace resulting from `__int128`. I will report thest presently.  
  
Thoughts... Suggestions... ?  
  
Cc: @mborland and @jzmaddock and @NAThompson

---

## Comment 7

> Username: mborland  
> Created_at: 2021-12-13 19:20:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-992792555  

@ckormanyos I am working on re-writing `test_arithmetic` to contextually disable any boost usage. That will resolve the standalone testing problem I think.

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2021-12-14 11:06:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-993429086  

> working on re-writing `test_arithmetic` to contextually disable any boost usage. That will resolve the standalone testing problem I think.  
  
Great! Thanks @mborland. As soon as this is/will be running, I think we have a really good standpoint on standalone.  
  
Optionally... I will take a look at remaining `__int128` warnings, and check out any other elevated conversion warnings. i will also investigate the situation with string-streaming.  
  
Again, this stuff is mostly optional and not needed for standalone (with the exception of the `__int128` warnings on `-pedantic` that we did identify as a goal). So I will get back to you if any changes are required on that detail.  
  
The higher conversin warnings and string-streaming investigations are not required but I will try to find a chance to look into them anyway.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-12-15 18:44:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-995070704  

Hi @mborland I did a quick check of bin-float, dec-float, gmp-float and mpfr-float.  
  
Many thanks. I find in this quick check that the all formerly remaining  `__int128` warnings on `-pedantic` are now gone.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-12-18 12:17:48 UTC  
> Updated_at: 2021-12-18 12:20:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-997194474  

Hi @mborland we might want to merge from develop again. We observe that @jzmaddock is excellently handling issue-after-issue.  
  
When I merged develop over to my `standalone_chris` branch, I found a merge conflict or two in `misc.hpp`. In addition to all the changes from `mborland:standalone`, I did, however, also have a small number of changes handling conversion warnings in `cpp_dec_float`. So that might have complicated my particular merge.  
  
It might be easier to merge to  `mborland:standalone_fixes` directly from develop first --- or optionally from my branch remotely. Either way, the few changes I had/have for `-Wconversion` and `-Wsign-conversion` I would also be happy to reproduce any time if needed.

---

## Comment 11

> Username: mborland  
> Created_at: 2021-12-19 10:35:17 UTC  
> Updated_at: 2021-12-19 10:35:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-997368208  

@ckormanyos This branch is now up to date with all the merge conflicts resolved. I have one failure in the standalone implementation of gmp_float -> long double conversions, but that should be fixed shortly.

---

## Comment 12

> Username: mborland  
> Created_at: 2021-12-19 17:13:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-997427724  

@jzmaddock Good call. Ran the new `gmp_float` to `long double` conversion through valgrind to validate the change.  
  
```  
==101192== Memcheck, a memory error detector  
==101192== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==101192== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info  
==101192== Command: ./log_adptr --leak-check=full  
==101192==   
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and h  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and a  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and a  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and c  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and c  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and s  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and s  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and t  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and i  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and i  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and j  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and l  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and l  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and m  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and x  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and x  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and y  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and f  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and f  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and d  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and d  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and e  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends14logged_adaptorINS2_9gmp_floatILj0EEEEELNS0_26expression_template_optionE1EEE and e  
No errors detected.  
==101192==   
==101192== HEAP SUMMARY:  
==101192==     in use at exit: 0 bytes in 0 blocks  
==101192==   total heap usage: 13,335 allocs, 13,335 frees, 607,350 bytes allocated  
==101192==   
==101192== All heap blocks were freed -- no leaks are possible  
==101192==   
==101192== For lists of detected and suppressed errors, rerun with: -s  
==101192== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
```  
  
Will fix the gmp_int conversions.

---

## Comment 13

> Username: mborland  
> Created_at: 2021-12-21 09:44:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-998626431  

@ckormanyos Do you see any large or outstanding issues that we should address here? CI is back to green with all these changes.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-12-21 10:43:33 UTC  
> Updated_at: 2021-12-21 10:45:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-998671616  

This is great @mborland !  
  
> Do you see any large or outstanding issues that we should address here? CI is back to green with all these changes.  
  
Functionally, all is OK.  
  
One thing you might want, ... or freely choose to disregard...  
  
I just did commit (via ci skip in my `standalone_chris` branch) a bunch of changes to handle conversion warnings in `cpp_bin_float`. Some of the casts I selected were quite difficult to choose due to all the massively templated code. I also added missing default cases to switches. If you would like to take a look at the casts and added `default` cases missing from switches, we could consider integrating my changes.  
  
It's your choice if you'd like to look at and potentially accept these corrections to conversoin warnings or not. If not, I am good to go at this point with the excellent state we have reached.  
  
If we decide to accept my corrections to conversoin warnings in Multiprecision, there is an associated little list of changes needed in Math that I can also present.  
  
Cc: @jzmaddock

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2021-12-21 14:58:26 UTC  
> Updated_at: 2021-12-21 14:59:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-998846877  

>> CI is back to green with all these changes.  
  
> my corrections to conversoin warnings  
  
Ummm... Alternatively, we could merge to develop immediately. If I would want to to work on elevated warning levels on certain compilers, I should be happy to do that in future branches.  
  
This would be more consistent with the topic of this PR which is going standalone.  
  
@mborland and @jzmaddock thoughts...?  
  
Cc: @NAThompson

---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-21 15:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/403#pullrequestreview-837603754  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
1574 |- 
1575 |-    return d;
1567 |+    return std::strtod(str(std::numeric_limits<double>::digits10 + (2 + 1), std::ios_base::scientific).c_str(), nullptr);
```

> Username: jzmaddock  
> Created_at: 2021-12-21 15:58:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773255722  

This is a pain in the butt, but can you check that this doesn't re-awaken this issue: https://github.com/boostorg/multiprecision/issues/167.  The problem is that the global locale can sometimes interpret strings in mighty weird ways :(


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-21 16:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/403#pullrequestreview-837612770  

📁 include/boost/multiprecision/gmp.hpp

```diff
 513 |+    ~gmp_char_ptr() noexcept 
 514 |+    { 
 515 |+       free(ptr_val);
```

> Username: jzmaddock  
> Created_at: 2021-12-21 16:06:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773262086  

Another pitfall here: gmp's allocation functions are replaceable, they will use malloc/free by default, but could be some other random function.  Sadly I think we need to use mp_get_memory_functions here to obtain a pointer to the correct deallocation function :(


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-21 16:19:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/403#pullrequestreview-837626368  

📁 include/boost/multiprecision/gmp.hpp

```diff
3698 |       };
3638 |-       static Real epsilon()
3699 |+       static constexpr Real epsilon() noexcept
```

> Username: jzmaddock  
> Created_at: 2021-12-21 16:19:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773271930  

Unfortunately we can't make this change: for variable precision types epsilon may vary at runtime, therefore this may **not** be `constexpr`, nor may it call `numeric_limits` (which may not even have a specialization if the type has variable precision).  Apologies, there may be a lack of testing on my part here: these members are documented requirements for Eigen, but I couldn't figure out which Eigen functions were using them :(  
  
I used boost::math::tools::digits as a convenient shortcut, it's going to be tricky to do this correctly without it, though not impossible.  
  
The other functions here have similar issues I'm afraid :(


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-21 16:26:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/403#pullrequestreview-837634096  

📁 include/boost/multiprecision/tommath.hpp

```diff
 700 |    {
 694 |-       *result = (std::numeric_limits<T2>::max)();
 701 |+       *result = 0;
```

> Username: jzmaddock  
> Created_at: 2021-12-21 16:26:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773277554  

Hold on, doesn't this change behaviour here?  
  
The intention is that values outside the range of the target type should saturate.

> Username: jzmaddock  
> Created_at: 2021-12-21 17:09:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773309011  

In fact what is the motivation here - it appears not to be related to standalone mode?

> Username: mborland  
> Created_at: 2021-12-22 13:46:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r773898583  

This section of code is all context driven by standalone. In the last merge it wasn't totally operable. I would agree that max or throwing a domain error would make sense, but this change is to match what `boost::lexical_cast` does to pass the arithmetic tests. I didn't want to change the behavior based on context.

> Username: jzmaddock  
> Created_at: 2021-12-22 17:14:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#discussion_r774046471  

Hmmm, I'm wondering if that code was wrong to begin with, I may need to write some more tests here...


---

## Comment 20

> Username: jzmaddock  
> Created_at: 2021-12-21 17:10:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-998951704  

>Ummm... Alternatively, we could merge to develop immediately. If I would want to to work on elevated warning levels on certain compilers, I should be happy to do that in future branches.  
  
+1, otherwise the diffs get too big!

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2021-12-21 18:29:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999001802  

>> merge to develop immediately  
  
> +1, otherwise the diffs get too big!  
  
Done. Merged to develop. Review comments to be handled in a future PR.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2021-12-21 19:30:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999039005  

>Review comments to be handled in a future PR.  
  
OK, but some of those look like proper breakages to me.

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2021-12-22 08:05:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999365929  

>> Review comments to be handled in a future PR.  
  
> OK, but some of those look like proper breakages to me.  
  
Uggghhh. Sorry that was not the intent and I had not realized that. I guess my intensity in the drive toward standalone got the better of me. I'd need help on some of those details.  
  
@mborland and @jzmaddock should we move forward with next PR or should I revert the changes?  
  
Sorry for any disruption I caused.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2021-12-22 11:52:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999519445  

>@mborland and @jzmaddock should we move forward with next PR or should I revert the changes?  
  
I think I would vote for reverting.  
  
>Sorry for any disruption I caused.  
  
No worries!

---

## Comment 25

> Username: mborland  
> Created_at: 2021-12-22 13:41:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999585747  

@ckormanyos I go on holiday starting tonight so I concur with reverting the merge.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2021-12-22 19:12:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999804026  

>> vote for reverting  
  
> concur with reverting the merge  
  
OK. Agreed. With one episode of back and forth, I have reverted to 75b17b943a6b9d02d5a2be8c439b3a3267dcba79  
  
This includes John's fix of #407 but reverts my recent commit/push from Matt's standalone_fixes branch. Due to the one episode of back-and-forth, I inadvertently fired off three CI runs.  
  
I was a bit shaky on the revert syntax, but i'm pretty sure I hit the right commit now. Could you please check @mborland and @jzmaddock ?  
  
Thank you.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2021-12-22 19:31:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-999814927  

Thanks Chris, I think you got it: https://github.com/boostorg/multiprecision/compare/75b17b943a6b9d02d5a2be8c439b3a3267dcba79..7a563f36f4868f56bcb7adf5ee4b4a972605870b

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2021-12-23 07:13:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/403#issuecomment-1000094318  

> I think you got it  
  
OK, Cool --- back on track.  
  
Thanks John. Thanks Matt. Let's pick this up maybe after the holidays where we left off...

---
