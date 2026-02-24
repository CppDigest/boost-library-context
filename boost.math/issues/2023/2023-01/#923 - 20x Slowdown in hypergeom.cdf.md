# #923 - 20x Slowdown in hypergeom.cdf [Closed]

> Username: mborland  
> Created at: 2023-01-23 16:14:40 UTC  
> Updated at: 2023-03-18 20:24:58 UTC  
> Closed at: 2023-01-29 12:20:26 UTC  
> Url: https://github.com/boostorg/math/issues/923  

See: https://github.com/scipy/scipy/issues/16079  
  
[Minimal example with benchmarks](https://github.com/scipy/scipy/issues/16079#issuecomment-1399806002)  
  
It's interesting that the slowdown is reproduced by C++11 vs C++14. I am hoping this will not evolve into a larger issue as we move to C++14 as the minimum standard.

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-23 17:55:42 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400749038  

Here are the values using current develop with different compilers:  
  
Apple Clang 14 w/libc++ using C++11 on M1  
```  
./hypergeom1  0.59s user 0.00s system 67% cpu 0.880 total  
```  
  
Apple Clang 14 w/libc++ using C++14 on M1  
```  
./hypergeom2  11.84s user 0.02s system 97% cpu 12.108 total  
```  
  
GCC 12 w/libstdc++ using C++11 on M1  
```  
./hypergeom3  0.70s user 0.01s system 72% cpu 0.983 total  
```  
  
GCC 12 w/libstdc++ using C++14 on M1  
```  
./hypergeom4  12.36s user 0.03s system 97% cpu 12.745 total  
```  
  
GCC 12 w/libstdc++ using C++17 on M1  
```  
./hypergeom5  12.44s user 0.03s system 98% cpu 12.660 total  
```  
  
GCC 12 w/libstdc++ using C++20 on M1  
```  
./hypergeom6  12.72s user 0.03s system 97% cpu 13.123 total  
```

---

## Comment 2

> Username: mborland  
> Created at: 2023-01-23 18:16:48 UTC  
> Updated at: 2023-01-23 18:20:46 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400777365  

And here are values on x86-64 (i7-1255U) using Ubuntu 20.04.5:  
  
GCC 10 w/libstdc++ using C++11  
```  
real	0m0.619s  
user	0m0.618s  
sys	0m0.000s  
```  
  
GCC 10 w/libstdc++ using C++14  
```  
real	0m6.886s  
user	0m6.881s  
sys	0m0.004s  
```  
  
GCC 10 w/libstdc++ using C++17  
```  
real	0m5.766s  
user	0m5.766s  
sys	0m0.000s  
```  
  
The x86-64 slowdown is 10x instead of 20x on M1.  
  
Edit: Using Boost 1.77 and 1.75 yield the same results.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-01-23 18:23:11 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400785787  

Hmmm...  
  
Couple ideas.  
  
- Try to rule out optimization level? Try `-Os` and maybe try `-O2` (which is not so radical)?  
- I've seen slowdowns with `constexpr` previously, but a factor of 20 seems huge.  
- the `clang++` compiler allows you to actually specify stuff like `-fconstexpr-steps=8` or also `-fconstexpr-depth=8`, which I expect wil lead to compilation  errors, but may give you insight into what is or is not all getting a `constexpr` hit?

---

## Comment 4

> Username: ckormanyos  
> Created at: 2023-01-23 18:24:07 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400786910  

Vague recolection about something `chrono`-esque going on in there.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2023-01-23 18:27:53 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400791461  

This is weird (and rather worrying), just stepping through the code I see nothing that would obviously change with std version - there's no constexpr stuff in there either @ckormanyos.  It is *very* computationally intensive though, lot's of table lookup of the prime numbers too.  Ah... I wonder if this could be a thread safety thing doing table lookup?  Some hidden locking going on maybe?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-01-23 18:31:06 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400795187  

Cygwin x86:  
  
C++11:  
  
real    0m0.734s  
user    0m0.640s  
sys     0m0.000s  
  
c++14:  
  
real    0m18.668s  
user    0m17.734s  
sys     0m0.016s  
  
Wow!

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-01-23 18:33:18 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400797464  

Reproduced with PDF as well as CDF (the former being called by the latter internally).

---

## Comment 8

> Username: jzmaddock  
> Created at: 2023-01-23 18:42:23 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400809748  

It looks like it might be related to prime number lookup - which uses std::array internally - were there any changes between c++11 and 14?  Also noted that boost::math::prime is NOT expanded inline in C++14 at least even though it's a fairly trivial function.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2023-01-23 18:46:17 UTC  
> Updated at: 2023-01-23 18:47:18 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400813805  

> uses `std::array` internally - were there any changes between c++11 and 14  
  
There was something seemingly trivial involving the member `at()` (which may `throw`). It became `constexpr`, but that seems irrelevant?

---

## Comment 10

> Username: mborland  
> Created at: 2023-01-23 18:47:31 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400815122  

> It looks like it might be related to prime number lookup - which uses std::array internally - were there any changes between c++11 and 14? Also noted that boost::math::prime is NOT expanded inline in C++14 at least even though it's a fairly trivial function.  
  
The only thing that would have changed for `std::array` between C++11 and C++14 would be [aggregate initialization rules](https://en.wikipedia.org/wiki/C%2B%2B14). I doubt that would be in-play here.

---

## Comment 11

> Username: mborland  
> Created at: 2023-01-23 18:53:29 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400821216  

If you remove all of the contextual `constexpr`-ness from prime and make it all follow the C++11 `static const` and `inline` you get:  
  
C++11  
```  
./hypergeom1  0.69s user 0.01s system 74% cpu 0.947 total  
```  
  
C++14  
```  
./hypergeom1  0.65s user 0.01s system 81% cpu 0.810 total  
```  
  
C++17  
```  
./hypergeom1  0.65s user 0.01s system 67% cpu 0.991 total  
```

---

## Comment 12

> Username: jzmaddock  
> Created at: 2023-01-23 18:54:26 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400822368  

Yup, just found that too: it's the constexpr std::array's that are totally killing things.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2023-01-23 18:59:52 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400828521  

No change by making them C style arrays.

---

## Comment 14

> Username: mborland  
> Created at: 2023-01-23 19:00:10 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400828854  

> Yup, just found that too: it's the constexpr std::array's that are totally killing things.  
  
Maybe because the storage isn't treated as `static`? We don't get that as an option until C++23

---

## Comment 15

> Username: ckormanyos  
> Created at: 2023-01-23 19:07:51 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1400838994  

> Maybe because the storage isn't treated as `static`  
  
Sometimes with big tables such as `static const std::array<std::uint16_t, 3458> a3` I've seen compilers make a lot of intermediate code in order to handle the `constexpr`-ness. I do not know if this is a compelling factor in this case.  
  
This is a pending issue that I have on microcontroller code in completely different domains. I tend to avoid `constexpr` talbes with more than 8-16 elements.

---

## Comment 16

> Username: mborland  
> Created at: 2023-01-23 23:13:48 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1401135207  

For posterity here's a comparison of the code generated between the C++11 and C++14 version  
  
https://godbolt.org/z/zds83adMd

---

## Comment 17

> Username: ckormanyos  
> Created at: 2023-01-24 07:49:58 UTC  
> Updated at: 2023-01-24 07:50:34 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1401508481  

> For posterity here's a comparison of the code generated between the C++11 and C++14 version  
  
Thanks Matt.  
  
Is there a Boost.Math/Multiprecision coding rule emerging?  
  
For me it's actually more than posterity. I'm wondering what the take-away is here?  
- Use `constexpr` `std::array<>` with caution?  
- Use only small `constexpr` `std::array<>`s with dimension limited to _what_?  
- Don't use `constexpr` `std::array<>`?  
- Use `constexpr` `std::array<>` if (and only if) the use case is known to be unequivocally and exclusively `constexpr` in its context?  
  
I know that we can't hash this all out here, but I've encountered (and forgotten) and encountered (and forgotten) this problem a few times.  
  
Cc: @jzmaddock and @mborland

---

## Comment 18

> Username: mborland  
> Created at: 2023-01-24 17:06:37 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1402287917  

That's a good question. I guess I have falsely assumed that a `constexpr std::array<>` didn't have to be regenerated every time the function was called. In the liked PR I should be able to get a wrapper class working that would allow us to use `static constexpr std::array<>` with C++14 instead of C++23. It's kind of an ugly solution but it would be the best of both worlds for the end user. It helps that we are moving to C++14 in the next release anyway because it would definitely break C++11 `constexpr` guarantees.

---

## Comment 19

> Username: valiko-ua  
> Created at: 2023-03-17 17:50:35 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474200572  

@mborland After reading this: https://quuxplusone.github.io/blog/2022/07/08/inline-constexpr/  
I wonder if we should mark those arrays as static constexpr inline?  
Won't we have multiple copies of those arrays in every compilation unit if we don't mark them inline?

---

## Comment 20

> Username: jzmaddock  
> Created at: 2023-03-17 17:58:22 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474210144  

These aren't global variables - they're members of a class template, so I *think* and certainly hope, they should be merged at link time ever since C++98.  Wouldn't hurt to check though!

---

## Comment 21

> Username: valiko-ua  
> Created at: 2023-03-17 18:05:12 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474217824  

> These aren't global variables - they're members of a class template, so I _think_ and certainly hope, they should be merged at link time ever since C++98. Wouldn't hurt to check though!  
  
I mean arrays **a1**, **a2**, **a3** - they are global variables declared in namespace **boost::math::detail**, outside of function template **prime**.

---

## Comment 22

> Username: jzmaddock  
> Created at: 2023-03-17 18:30:32 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474243787  

> I mean arrays a1, a2, a3 - they are global variables declared in namespace boost::math::detail, outside of function template prime.  
  
No more, they're class members: https://github.com/boostorg/math/blob/53766896dded5ed9ce4953415250510e5161a99b/include/boost/math/special_functions/prime.hpp#L22

---

## Comment 23

> Username: valiko-ua  
> Created at: 2023-03-17 18:46:41 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474265491  

Oops, sorry, I looked at the linked PR at the top.

---

## Comment 24

> Username: valiko-ua  
> Created at: 2023-03-18 20:24:58 UTC  
> Url: https://github.com/boostorg/math/issues/923#issuecomment-1474984072  

Does it make sense to move array values to some macros (A1_VALUES, A2_VALUES, A3_VALUES) to avoid repetition and reduce file size?
