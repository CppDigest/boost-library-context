# #28 - `endian_arithmetic` should also be a POD or provide the constructors [Closed]

> Username: viboes  
> Created at: 2018-02-11 22:31:07 UTC  
> Updated at: 2023-03-13 17:55:24 UTC  
> Closed at: 2020-10-07 17:55:00 UTC  
> Url: https://github.com/boostorg/endian/issues/28  

In http://www.boost.org/doc/libs/1_60_0/libs/endian/doc/arithmetic.html is said  
  
> In C++03, endian_arithmetic does not meet the requirements for POD types because it has constructors, private data members, and a base class.  
  
and  
  
>    // if BOOST_ENDIAN_FORCE_PODNESS is defined && C++11 PODs are not  
      // available then these two constructors will not be present  
      endian_arithmetic() noexcept = default;  
      endian_arithmetic(T v) noexcept;  
  
As the class has a base class the constructor doesn't help to make the type a C++03 POD.  
  
While I don't like to repeat the code it is a pity that `endian_arithmetic` type is not a POD, as it is used in a POD context, to transport data between machines with different endianness.  
  
All the code in `endian_buffer` could be included inside `endian_arithmetic` via a common macro, and make this type a POD.  
  
If you don't want to do that, there is no major reason to remove the constructors in C++03 when BOOST_ENDIAN_FORCE_PODNESS  is defined.  
  
Relying in UB is not something I would advice to use in Boost.

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-12 06:56:22 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-364840965  

An alternative to inheritance is composition. IMO, a `endian_arithmetic` is not really a `endian_buffer` ("is-a"), but better it should contains one as representation. The interface of `endian_buffer` is not so big and delegating to the representation is not so hard.  
  
For the operators part, I would move all of them directly to `endian_arithmetic`.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-28 15:21:20 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-487388859  

I've moved the operators to `endian_arithmetic`, but removing the inheritance from `endian_buffer` is a breaking change, and I don't know how to evaluate its impact.

---

## Comment 3

> Username: taam  
> Created at: 2020-07-09 10:01:45 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-656034827  

This is causing trouble for us as well, we build packed structs (using `__attribute__((packed))`) containing the arithmetic types, but GCC does not like it:  
  
> error: ignoring packed attribute because of unpacked non-POD field 'boost::endian::[...]  
  
Full example: https://wandbox.org/permlink/dbDdKqxSrsSHWU6m

---

## Comment 4

> Username: pdimov  
> Created at: 2020-07-09 13:54:04 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-656141727  

Thanks for the example, it provides me with an excuse to remove the inheritance. However, out of curiosity, is the `double` in the middle supposed to be native-endian? Shouldn't it be `big_float64_t`, or perhaps `little_float64_t`?

---

## Comment 5

> Username: taam  
> Created at: 2020-07-09 16:38:52 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-656231688  

> However, out of curiosity, is the `double` in the middle supposed to be native-endian? Shouldn't it be `big_float64_t`, or perhaps `little_float64_t`?  
  
That's true, was not from real code, just wanted to put in an arbitrary type that would cause some padding.  
  
Btw. GCC does not complain, if the class that uses inheritance and private members is declared with `__attribute__((packed))`, but that's probably more a platform dependent hack, not sure. (It's how we did it in the past using a very old patched version of the library.)

---

## Comment 6

> Username: pdimov  
> Created at: 2020-07-09 19:05:41 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-656299491  

Interestingly,  
```  
struct foo {  
	big_uint16_buf_t a;  
	double b;  
	big_uint16_buf_t c;  
} __attribute__((packed));  
```  
gives the same warnings, and yet  
```  
static_assert(std::is_pod<big_uint16_buf_t>::value, "");  
```  
passes (as it should, in C++11 and later.)  
  
It seems that `__attribute__((packed))` has its own idea of PODness.

---

## Comment 7

> Username: taam  
> Created at: 2020-07-09 19:45:22 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-656316516  

Indeed, also see: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=89519

---

## Comment 8

> Username: pdimov  
> Created at: 2020-07-28 15:30:43 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-665109623  

I just now realize that the packing actually works, because the static_assert for the size passes. So the only problem is that it warns (incorrectly) and there doesn't seem to be any way to turn that warning off.

---

## Comment 9

> Username: pdimov  
> Created at: 2020-10-07 17:55:00 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-705098337  

Should be fixed in develop.

---

## Comment 10

> Username: taam  
> Created at: 2020-10-12 18:12:59 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-707272055  

Looking good, thanks!  
  
(Tested it with our codebase, it works not quite as smooth as the applying `__attribute__((packed))` to all endian_* classes hack, as you can't use constructors any more, but at least it allows getting rid of the GCC warnings with standard boost.)  
  
I noticed that of the 3 specializations of `endian_buffer` only the unaligned version was changed, what about the aligned versions?

---

## Comment 11

> Username: pdimov  
> Created at: 2020-10-12 18:21:46 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-707276119  

I don't think packing is useful with aligned types. In fact I had to change the `double` in the test to an unaligned buffer type because the tests were failing on MinGW.  
  
If someone reports this as a problem with aligned types, I'll change them as well, but I don't think anyone will.

---

## Comment 12

> Username: taam  
> Created at: 2020-10-12 18:44:34 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-707285827  

That's probably true. Slightly inconsistent now, but as long as packing is the only use case for the macros, I guess it doesn't matter.

---

## Comment 13

> Username: nebkat  
> Created at: 2023-03-13 14:05:46 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-1466208121  

Could something similar to this be added?  
  
https://github.com/nebkat/endian/commit/f57f0d19447ee85086fe440b58a963cb70165524  
  
Not sure I fully understood the discussion above but I have found no other way to disable the warnings, and I heavily rely on the constructors so the `BOOST_ENDIAN_NO_CTORS` is not a great option for me.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-03-13 17:00:35 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-1466533701  

I don't think that the aligned types should ever be `__attribute__((packed))`, as that would make them unaligned.

---

## Comment 15

> Username: nebkat  
> Created at: 2023-03-13 17:55:24 UTC  
> Url: https://github.com/boostorg/endian/issues/28#issuecomment-1466632560  

Apologies I applied that macro indiscriminately in buffers.hpp, I am only using the unaligned types.  
  
Corrected in https://github.com/nebkat/endian/commit/36757d1cab888d50f50f7a0984092cde895ce019
