# #39 - Is float/double serialization supported ? [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-12-19 10:49:07 UTC  
> Updated at: 2019-12-29 17:53:45 UTC  
> Closed at: 2019-12-20 00:34:21 UTC  
> Url: https://github.com/boostorg/endian/issues/39  

Hi,  
  
I am a bit confused by what the documentation about Boost.Endian reads. I read that:  
  
> Why is there no floating point support?  
> An attempt was made to support four-byte floats and eight-byte doubles, limited to IEEE 754 (also known as ISO/IEC/IEEE 60559) floating point and further limited to systems where floating point endianness does not differ from integer endianness. Even with those limitations, support for floating point types was not reliable and was removed. For example, simply reversing the endianness of a floating point number can result in a signaling-NAN. For all practical purposes, binary serialization and endianness for integers are one and the same problem. That is not true for floating point numbers, so binary serialization interfaces and formats for floating point does not fit well in an endian-based library.  
  
However in the change log we can see:  
  
> Changes in 1.71.0  
> - Added support for float and double  
  
Let's say I am only interested in serializing a float/double from one machine to another, over the wire, which may use different architecture. Is it safe to do like for ints (ie convert it to big endian and then use endian_buffer to read/write it on the socket on both sides), or is it indeed doomed to fail in the general case as explained in the first paragraph ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2019-12-19 11:02:32 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567443116  

Please note that my question is only for plateform using IEEE 754. If that changes anything to the answer, the possible plateforms candidate would be what you can expect to find in a classical cloud server environment (classical, not HPC, so I would say either x64 or ARM64).

---

## Comment 2

> Username: pdimov  
> Created at: 2019-12-19 14:50:48 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567519999  

Since 1.71 `float`/`double` are supported in `endian_buffer` and `endian_arithmetic`. They still aren't supported in `endian_reverse`, which returns by value, for the reasons outlined in the FAQ entry.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2019-12-20 00:34:21 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567737699  

Ok thanks, so from what I understand, for people like me interested by serializing/deserializing on the wire, boost >= 1.71 will be ok (using endian_buffer). I don't need to have endianness conversion from double to double, just from double to char, and char to double.  
  
Thanks !

---

## Comment 4

> Username: pdimov  
> Created at: 2019-12-20 00:45:37 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567740056  

As long as the endianness of float/double is the same as that of the same-sized integer type, yes.

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created at: 2019-12-20 01:34:24 UTC  
> Updated at: 2019-12-20 01:34:39 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567749855  

Would it make sense that Boost.Endian itself ensures this somehow by not defining the float/double buffer types `#if __BYTE_ORDER__ != __FLOAT_WORD_ORDER__` ?

---

## Comment 6

> Username: pdimov  
> Created at: 2019-12-20 01:46:45 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-567752308  

Maybe, I'm not sure. Wikipedia says "However, on modern standard computers (i.e., implementing IEEE 754), one may in practice safely assume that the endianness is the same for floating-point numbers as for integers, making the conversion straightforward regardless of data type."  
  
If this is a practical problem, it might need addressing in a more thorough manner by defining two "native" endianness constants, one for integers and one for floats. But I'm not sure it is.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-12-29 17:53:45 UTC  
> Url: https://github.com/boostorg/endian/issues/39#issuecomment-569527549  

On reflection, `__FLOAT_WORD_ORDER__` is not the right macro here. It's about the _word_ order in "multiword floating point numbers" (whatever that means - I assume `long double` that consists of two `double`s), not about the byte order in floats. Since there's no separate `__FLOAT_BYTE_ORDER__`, I assume no platform supported by GCC has floats and integers differing in endianness.
