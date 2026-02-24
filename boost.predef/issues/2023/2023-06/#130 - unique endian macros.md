# #130 - unique endian macros [Open]

> Username: gpeterhoff  
> Created at: 2023-06-07 12:25:23 UTC  
> Updated at: 2023-06-16 07:15:01 UTC  
> Url: https://github.com/boostorg/predef/issues/130  

Hello,  
often the mistake is made (happened to me too) to consider only 2 cases when checking the endianess, eg:  
  
struct foo  
{  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE)  
 uint32_t a : 16;  
 uint32_t b : 16;  
#else  
 uint32_t b : 16;  
 uint32_t a : 16;  
#endif  
};  
  
because the else branch is not necessarily BOOST_ENDIAN_BIG_BYTE_AVAILABLE. More correctly, this example must read:  
  
struct foo  
{  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE)  
 uint32_t a : 16;  
 uint32_t b : 16;  
#elif defined(BOOST_ENDIAN_BIG_BYTE_AVAILABLE)  
 uint32_t b : 16;  
 uint32_t a : 16;  
#else  
 #error or  
 specializations for BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE/BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE  
#endif  
};  
  
This is error-prone and usually overlooked.  
Therefore it would make sense to provide unique macros that guarantee that ONLY the contrary endianess is used in the else-branch, eg [endian.hpp.txt](https://github.com/boostorg/predef/files/11676671/endian.hpp.txt). Now we can use correctly:  
  
struct foo  
{  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_UNIQUE_AVAILABLE)  
 uint32_t a : 16;  
 uint32_t b : 16;  
#else  
 uint32_t b : 16;  
 uint32_t a : 16;  
#endif  
};  
  
But this means that these unique-macros are actually used. It would probably make more sense to "teach" this behavior to the existing macros.  
  
thx & regards  
Gero

---

## Comment 1

> Username: andry81  
> Created at: 2023-06-07 13:22:56 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1580814548  

The last example looks like the first example. Why you think it would work differently?  
  
https://www.boost.org/doc/libs/1_82_0/boost/predef/other/endian.h  
  
```cpp  
  
#if BOOST_ENDIAN_BIG_BYTE  
#   define BOOST_ENDIAN_BIG_BYTE_AVAILABLE  
#endif  
#if BOOST_ENDIAN_BIG_WORD  
#   define BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE  
#endif  
#if BOOST_ENDIAN_LITTLE_BYTE  
#   define BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE  
#endif  
#if BOOST_ENDIAN_LITTLE_WORD  
#   define BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE  
#endif  
  
#define BOOST_ENDIAN_BIG_BYTE_NAME "Byte-Swapped Big-Endian"  
#define BOOST_ENDIAN_BIG_WORD_NAME "Word-Swapped Big-Endian"  
#define BOOST_ENDIAN_LITTLE_BYTE_NAME "Byte-Swapped Little-Endian"  
#define BOOST_ENDIAN_LITTLE_WORD_NAME "Word-Swapped Little-Endian"  
```

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-06-07 16:11:59 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1581138885  

You have overlooked the UNIQUE in the macro. In the first example, as I said, several cases can occur in the else branch:  
- BOOST_ENDIAN_BIG_BYTE_AVAILABLE (was probably meant)  
- BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE or BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE (eg DPD, which is probably not meant)  
  
With the UNIQUE-macros these ambiguities are excluded.  
  
thx & regards  
Gero

---

## Comment 3

> Username: andry81  
> Created at: 2023-06-08 01:31:32 UTC  
> Updated at: 2023-06-08 01:38:55 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1581755473  

> You have overlooked the UNIQUE in the macro.   
  
I am not.  
  
> With the UNIQUE-macros these ambiguities are excluded.  
  
It still not clear how.  
  
If you wrote something like:   
  
```cpp  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_LITTLE_WORD_LITTLE_DWORD_LITTLE_QWORD_BLABLABLA_AVAILABLE)  
```  
  
then, it would be more unique.

---

## Comment 4

> Username: gpeterhoff  
> Created at: 2023-06-08 03:32:30 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1581837272  

I thought my (brief) comments would be sufficient. But I would like to explain it to you in detail:  
  
// WORD ?  
#if defined(BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE) || defined(BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_WORD_AVAILABLE  
#endif  
BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE or BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE is available - independent of BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE or BOOST_ENDIAN_BIG_BYTE_AVAILABLE  
  
  
// BYTE ?  
#if defined(BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE) || defined(BOOST_ENDIAN_BIG_BYTE_AVAILABLE))  
#define BOOST_ENDIAN_BYTE_AVAILABLE  
#endif  
BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE or BOOST_ENDIAN_BIG_BYTE_AVAILABLE is available - independent of BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE or BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE  
  
// unique WORD or BYTE ?  
#if (defined(BOOST_ENDIAN_WORD_AVAILABLE) && !defined(BOOST_ENDIAN_BYTE_AVAILABLE)) || /* WORD only */ \  
(!defined(BOOST_ENDIAN_WORD_AVAILABLE) && defined(BOOST_ENDIAN_BYTE_AVAILABLE)) /* BYTE only */  
#define BOOST_ENDIAN_UNIQUE_AVAILABLE  
#endif  
BOOST_ENDIAN_UNIQUE_AVAILABLE is defined only if BOOST_ENDIAN_WORD_AVAILABLE or BOOST_ENDIAN_BYTE_AVAILABLE is defined - exclusive or.  
If both/no definitions exist, BOOST_ENDIAN_UNIQUE_AVAILABLE is not defined.  
This makes it possible to clearly distinguish whether the endianness is WORD- or BYTE-based.  
  
  
// unique WORD  
#if defined(BOOST_ENDIAN_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_WORD_AVAILABLE)  
#define BOOST_ENDIAN_WORD_UNIQUE_AVAILABLE  
#endif  
helper, is only defined if the unique endianness is WORD-based  
  
// unique BYTE  
#if defined(BOOST_ENDIAN_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_BYTE_UNIQUE_AVAILABLE  
#endif  
helper, is only defined if the unique endianness is BYTE-based  
  
  
// unique LITTLE_WORD  
#if defined(BOOST_ENDIAN_WORD_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_LITTLE_WORD_UNIQUE_AVAILABLE  
#endif  
is only defined if the unique endianness is WORD-based and LITTLE_WORD  
  
// unique BIG_WORD  
#if defined(BOOST_ENDIAN_WORD_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_BIG_WORD_UNIQUE_AVAILABLE  
#endif  
is only defined if the unique endianness is WORD-based and BIG_WORD  
  
This can rule out that there is a BYTE-based endianness.  
If BOOST_ENDIAN_LITTLE_WORD_UNIQUE_AVAILABLE is defined, the complement (#else) BOOST_ENDIAN_BIG_WORD_UNIQUE_AVAILABLE is mandatory.  
If BOOST_ENDIAN_BIG_WORD_UNIQUE_AVAILABLE is defined, the complement (#else) BOOST_ENDIAN_LITTLE_WORD_UNIQUE_AVAILABLE is mandatory.  
  
  
// unique LITTLE_BYTE  
#if defined(BOOST_ENDIAN_BYTE_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_LITTLE_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_LITTLE_BYTE_UNIQUE_AVAILABLE  
#endif  
is only defined if the unique endianness is BYTE-based and LITTLE_BYTE  
  
// unique BIG_BYTE  
#if defined(BOOST_ENDIAN_BYTE_UNIQUE_AVAILABLE) && defined(BOOST_ENDIAN_BIG_BYTE_AVAILABLE)  
#define BOOST_ENDIAN_BIG_BYTE_UNIQUE_AVAILABLE  
#endif  
is only defined if the unique endianness is BYTE-based and BIG_BYTE  
  
This can be ruled out that a WORD-based endianness is present.  
If BOOST_ENDIAN_LITTLE_BYTE_UNIQUE_AVAILABLE is defined, the complement (#else) BOOST_ENDIAN_BIG_BYTE_UNIQUE_AVAILABLE is mandatory.  
If BOOST_ENDIAN_BIG_BYTE_UNIQUE_AVAILABLE is defined, the complement (#else) BOOST_ENDIAN_LITTLE_BYTE_UNIQUE_AVAILABLE is mandatory.  
  
cu  
Gero

---

## Comment 5

> Username: andry81  
> Created at: 2023-06-08 21:17:39 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1583356584  

```cpp  
#if defined(BOOST_ENDIAN_LITTLE_WORD_BYTE_AVAILABLE) || defined(BOOST_ENDIAN_BIG_WORD_BYTE_AVAILABLE)  
```  
Why you need to check both? Either you use little or big endian.  
  
Your `unique` term does not mean unique actually, it means more like `not nested endian`. The `unique` here would be a specific combination of byte and word endians.  
  
These lines of code is quite hard to read mess. Why not write directly?  
  
```cpp  
#if BOOST_ENDIAN_BIG_BYTE || BOOST_ENDIAN_LITTLE_BYTE  
#  define BOOST_ENDIAN_BYTE_UNIQUE_AVAILABLE  
#  if !BOOST_ENDIAN_BIG_WORD  
#    if !BOOST_ENDIAN_LITTLE_WORD  
#      define BOOST_ENDIAN_UNIQUE_AVAILABLE  
//...  
#endif  
#if BOOST_ENDIAN_BIG_WORD || BOOST_ENDIAN_LITTLE_WORD  
#  define BOOST_ENDIAN_WORD_UNIQUE_AVAILABLE  
#  if !BOOST_ENDIAN_BIG_BYTE  
#    if !BOOST_ENDIAN_LITTLE_BYTE  
#      define BOOST_ENDIAN_UNIQUE_AVAILABLE  
//...  
#endif  
```  
  
It would be much more readable.

---

## Comment 6

> Username: gpeterhoff  
> Created at: 2023-06-16 03:16:28 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1594030080  

> Why you need to check both? Either you use little or big endian.  
  
This is exactly the conclusion. It is NOT guaranteed that in the case  
- little-end the complement big-end  
- big-end is the complement little-end  
  
because other combinations can occur  
- WORD  
- BYTE  
  
https://en.cppreference.com/w/cpp/types/endian (mixed-end, PDP).  
With the UNIQUE macros it can be guaranteed that ONLY the complement in the else-branch is used.  
  
thx  
Gero

---

## Comment 7

> Username: andry81  
> Created at: 2023-06-16 07:14:20 UTC  
> Updated at: 2023-06-16 07:15:01 UTC  
> Url: https://github.com/boostorg/predef/issues/130#issuecomment-1594221802  

> It is NOT guaranteed that in the case  
  
It does not guarantee either in your case, because you always need to write the else branch no matter what macro you are using. In both cases you can forget that. So it still is not clear why this need.
