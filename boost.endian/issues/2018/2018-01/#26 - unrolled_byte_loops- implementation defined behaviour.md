# #26 - unrolled_byte_loops: implementation defined behaviour? [Closed]

> Username: federkamm  
> Created at: 2018-01-15 18:49:53 UTC  
> Updated at: 2018-12-06 20:16:58 UTC  
> Closed at: 2018-12-06 20:16:58 UTC  
> Url: https://github.com/boostorg/endian/issues/26  

I'm just checking if I can use your library in my project and for my understanding of the standard it is very unportable.  
  
For signed T, unrolled_byte_loops<T,...>::load_big/little use bitwise or on a signed value.  The behaviour of this is well defined (it operates on the bit representation) but since the bit representation of negative values can differ on different architectures, the resulting is unpredictable (more or less).  Are you sure that your code works on platforms with one-complement or sign-magnitude representation?  (Just a question, as I don't intend to use your code with signed types.)  
  
What I worry more about is that unrolled_byte_loops::store_big/little uses a static_cast to char from a potentially larger type (signed, or in my case unsigned).  As far as I can tell, only the cast to an unsigned char is well defined but the cast to an signed type is implementation defined.  I know that char, signed char and unsigned char are three distinct types and I think I once red, that the cast to char would also work, but I couldn't find anything in the standard about it.  Are you sure the cast to char is really well defined and not just implementation defined?  If not, would it hurt to use an unsigend char here?  I'd feel better with that.

---

## Comment 1

> Username: federkamm  
> Created at: 2018-01-15 19:07:43 UTC  
> Url: https://github.com/boostorg/endian/issues/26#issuecomment-357765745  

And by the way: I just read in the standard in 5.8 [expr.shift]. The left and right shifts you use on signed types T in load/store_big/little also rely on implementation defined behaviour. (right shifting a negative number is implementation defined and right shifting a positive signed number which overflows the range of the signed type but stays in the range of the corresponding unsinged type yields a conversion from an unsigned integral to a signed one that is implementation defined by 4.7 [conv.integral], 3.)

---

## Comment 2

> Username: federkamm  
> Created at: 2018-01-15 19:10:53 UTC  
> Url: https://github.com/boostorg/endian/issues/26#issuecomment-357766373  

My suggestion would be to use unsigend char in store_big/little (or proof that the cast to char is well-defined) and to document that the use of signed types relyes on implementation defined behaviour and list some architectures/compilers for which the behaviour is as expected (and what the expected behaviour is for signed types).

---

## Comment 3

> Username: pdimov  
> Created at: 2018-01-17 18:00:15 UTC  
> Url: https://github.com/boostorg/endian/issues/26#issuecomment-358389253  

This too has been fixed: https://github.com/boostorg/endian/commit/134b22537820fb21de506aa83d96203ac7c4514e

---

## Comment 4

> Username: pdimov  
> Created at: 2018-01-17 18:20:34 UTC  
> Updated at: 2018-01-17 18:22:18 UTC  
> Url: https://github.com/boostorg/endian/issues/26#issuecomment-358395315  

You do have a point about the conversion to `char` though. Should probably be `unsigned char`. The relevant portions of the standard are [conv.integral]/2:  
  
> If the destination type is unsigned, the resulting value is the least unsigned integer congruent to the source integer (modulo 2n where n is the number of bits used to represent the unsigned type).  
  
and /3:  
  
> If the destination type is signed, the value is unchanged if it can be represented in the destination type; otherwise, the value is implementation-defined.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-12-06 20:16:58 UTC  
> Url: https://github.com/boostorg/endian/issues/26#issuecomment-445014975  

`char` has been replaced with `unsigned char` in https://github.com/boostorg/endian/commit/dafb9b1795f56c4c3ea594f337eb24d8c2128b32. If anything else remains, please reopen.
