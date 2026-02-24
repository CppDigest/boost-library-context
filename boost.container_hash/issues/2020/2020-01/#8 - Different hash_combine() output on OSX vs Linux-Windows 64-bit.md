# #8 - Different hash_combine() output on OSX vs Linux/Windows 64-bit [Closed]

> Username: e4lam  
> Created at: 2020-01-06 19:45:10 UTC  
> Updated at: 2021-10-15 16:07:31 UTC  
> Closed at: 2021-10-12 20:37:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8  

On macOS, `size_t` and `uint64_t` are different types. Thus, different `hash_combine_impl()` methods get called on 64-bit Linux/Windows vs macOS, which result in different outputs.  
  
```  
#include <boost/container_hash/hash.hpp>  
#include <iostream>  
  
int main()  
{  
    std::size_t x = 0, y = 0;  
    boost::hash_combine(x, y);  
    std::cout << std::hex << x << "\n";  
    // Linux/Windows: e6546b64  
    // macOS: 9e3779b9  
    return 0;  
}  
```

---

## Comment 1

> Username: mclow  
> Created at: 2020-01-06 20:31:35 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-571302032  

I agree with what you said. However, I don't see that this is a bug.  
Since they're different types, you get different results.

---

## Comment 2

> Username: e4lam  
> Created at: 2020-01-07 05:48:24 UTC  
> Updated at: 2020-01-07 06:02:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-571445517  

@mclow Looking at the specializations of `hash_combine_impl()` at https://github.com/boostorg/container_hash/blob/8a7fc581ed987fde8cae4e7145378d23b9b6b351/include/boost/container_hash/hash.hpp#L337   
  
I would find it very surprising that this behaviour was intended because it looks like the purpose of those uint32_t/uint64_t specializations are to do something special and they're never used on macOS (but used on Linux, Windows, and other sane platforms where `size_t` is the same as the corresponding same sized `uint*_t` type).

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-07 12:19:51 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-571564076  

I also think that the intent of this code is "if size_t is 32 bit, do this, if it's 64 bit, do that, otherwise fall back" rather than "if size_t is the exact same type as uint32_t, ..."

---

## Comment 4

> Username: zy-ph  
> Created at: 2021-02-11 10:06:24 UTC  
> Updated at: 2021-02-11 10:09:23 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-777331813  

> Looking at the specializations of `hash_combine_impl()` a  
  
I am also looking at them and I noticed the line  
`        template <typename SizeT>  
`  
was removed from both specializations (compared to 1.59) . So they actually are not specializations anymore.  
These 'sepcializations' should be prefixed with 'template <>' imho.

---

## Comment 5

> Username: cscrimge  
> Created at: 2021-08-09 20:24:14 UTC  
> Updated at: 2021-08-09 20:24:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-895516916  

The problem I've found is not that you get different results, but that one produces _good_ results, and the other produces _terrible_ results.  
  
```C++  
#include <boost/container_hash/hash.hpp>  
#include <iostream>  
  
int main()  
{  
	for (std::size_t x = 0; x < 4; x++)  
	{  
		for (std::size_t y = 0; y < 4; y++)  
		{  
			std::size_t seed = 0;  
			boost::hash_combine(seed, x);  
			boost::hash_combine(seed, y);  
			std::cout << std::hex << seed << "\n";  
		}  
	}  
}  
```  
  
On Linux:  
```  
cb34c5ae03d8f098  
c6bf1a65fce686ec  
82dd87c0404c7690  
f5220b4d519d5ff4  
a21166ee20272944  
fded1426c293cc40  
2ec71ff6f0486f3c  
62c7f72323d12748  
faae3bd75cf61f0  
22eb7962ec6d3ef4  
f49fc683de4e9bf8  
9df88f23972fe3ec  
264e3cb26569632c  
b410dc8a74550cd8  
367dba0f10944fd4  
a41188cb06b10130  
```  
  
On OSX:  
```  
28cd94bfde  
28cd94bfd1  
28cd94bfd0  
28cd94bfd3  
28cd94bf1d  
28cd94bf12  
28cd94bf13  
28cd94bf10  
28cd94bf5c  
28cd94bf53  
28cd94bf52  
28cd94bf51  
28cd94be94  
28cd94be95  
28cd94be96  
28cd94be97  
```  
  
The end result is that our hash container, when using boost::hash_combine, is 60x slower on OSX than it is on Linux/Windows.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-08-10 18:23:33 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-896214645  

See https://github.com/boostorg/container_hash/pull/17.

---

## Comment 7

> Username: e4lam  
> Created at: 2021-10-15 16:07:31 UTC  
> Url: https://github.com/boostorg/container_hash/issues/8#issuecomment-944420703  

Thank you very much for the fix!
