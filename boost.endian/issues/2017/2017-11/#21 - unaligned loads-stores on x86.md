# #21 - unaligned loads/stores on x86 [Closed]

> Username: arvidn  
> Created at: 2017-11-04 11:45:48 UTC  
> Updated at: 2019-08-02 07:41:15 UTC  
> Closed at: 2018-01-17 17:59:10 UTC  
> Url: https://github.com/boostorg/endian/issues/21  

while validating my code in UBsan (-fsanitize=undefined) I learned that the endian buffers perform unaligned loads and stores on x86-family of machines.  
  
There are preprocessor checks [here](https://github.com/boostorg/endian/blob/develop/include/boost/endian/buffers.hpp#L280) and a few other places.  
  
### I suggest these checks be removed, and the code honor the C++ abstract machine's requirements.  
  
In my mind they are problematic because:  
  
* UBsan will flag these as invalid  
* they _are_ invalid [1].  
* the compiler is also free to play the "undefined behavior" card and do whatever it likes [2].  
  
Furthermore, I would expect the compiler to be able to identify a 4-byte or 8-byte ``memcpy()`` as a potentially unaligned store/load, and generate a ``mov`` (which the compiler knows allows unaligned access), but not to generate SSE instructions.  
  
I'm proposing that these special cases be removed  
  
[1]. my x86 compiler is free to generate SSE instructions that *do* require aligned access  
[2]. Imagine storing a value through a pointer (that the standard requires be aligned) and later check the pointer to see whether or not its last few bits are zero. The compiler may remove the check since it *must* be aligned, otherwise you invoked UB anyway.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-21 13:58:13 UTC  
> Url: https://github.com/boostorg/endian/issues/21#issuecomment-353357154  

I'm not convinced that the unaligned loads and stores are invalid here.  
  
Usually these `reinterpret_cast` tricks are invalid for another reason - object lifetime - but here the code creates an object of type `T` at the address before accessing it, so lifetime is fine.  
  
I don't know of any x86 compilers that would use aligned SSE loads and stores for such code. This would probably break half of the existing code in existence.  
  
This aside, I added `-fsanitize=undefined` g++ 7 and clang 5 configurations to Travis, along with more test coverage in buffer_test.cpp, and UBsan says nothing (assuming I hadn't done some mistake and it doesn't run.)  
  
If we're fixing a UBsan issue, I'd prefer that it be reproduced first.

---

## Comment 2

> Username: federkamm  
> Created at: 2018-01-17 09:24:22 UTC  
> Url: https://github.com/boostorg/endian/issues/21#issuecomment-358245628  

This is not "undefined behaviour" but "unspecified behaviour" (not as bad as undefined behaviour but still bad enough to fix it).  
  
By C++ 11 Std 5.2.10 [expr.reinterpret.cast] paragraph 7, reinterpret_cast<T*> equals static_cast<T*> of static_cast<void*>.  By 5.2.9 [expr.static.cast] paragraph 13, the static cast from void* to T* works, "If [...] A satisfies the alignment requeriment of T" where A is the address the void* points to. Otherwise "The result of any other such pointer conversion is unpecified".  
  
Therfore, you can't be sure the result of the expression gives you the value you expect, but at least you know it gives you any value and the rest of your programm will still work (can't get rationalized away because of undefined behaviour). However, since you don't know what address exactly gets accessed it could yield SIGSEG and the memory sanitizer could fail (that might have happened to the OP).  
  
If the value would have been "implementation defined" you could use the code macro-guarded for different compilers that define the desired behaviour (in opposite to different platforms).  Even despite it's "unspecified behaviour" still some compiler could define a behaviour and you still could use the compiler-guard for them.  
  
Note that recent g++ and clang produce optimal machine code (unaligned mov + possibly bswap) in -O2 (clang from -Og) for code like  
  
uint32_t load_big_uint32(uint8_t const * x) {  
  return x[0] << 24UL | x[1] << 16 UL| x[2] << 8UL | x[3] << 0UL;  
}  
  
when you read 2 (clang only), 4 or 8 bytes. See: https://godbolt.org/g/HehhCs

---

## Comment 3

> Username: pdimov  
> Created at: 2018-01-17 17:59:10 UTC  
> Url: https://github.com/boostorg/endian/issues/21#issuecomment-358388926  

I eventually managed to get `-fsanitize=undefined` to work (by which I mean to fail the test https://github.com/boostorg/endian/commit/62802fee96f002134719d02158c6fe859847fd9b) and then fixed the unaligned accesses in https://github.com/boostorg/endian/commit/e93f6a22703249f0ebfff25302147c64965d5907.

---

## Comment 4

> Username: Flamefire  
> Created at: 2019-08-02 07:40:39 UTC  
> Updated at: 2019-08-02 07:41:15 UTC  
> Url: https://github.com/boostorg/endian/issues/21#issuecomment-517594108  

I know this is closed already but https://www.boost.org/users/history/version_1_67_0.html does not mention these changes although they actually do fix undefined behavior (it actually is undefined, not just unspecified) and **can** and **does** indeed lead to crashes.  
  
> I don't know of any x86 compilers that would use aligned SSE loads and stores for such code.  
  
I came across such an example in https://stackoverflow.com/a/46790815/1930508 and created a reproducer on godbolt using boost::endian: https://godbolt.org/z/GDipuT  
  
The example is very straight-forward: Simply sum all entries of an unaligned array of boost endian buffers and it will crash.  
  
So I'd give this some more exposure.
