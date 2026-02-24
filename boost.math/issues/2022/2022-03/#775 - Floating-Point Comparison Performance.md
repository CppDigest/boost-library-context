# #775 - Floating-Point Comparison Performance [Open]

> Username: mborland  
> Created at: 2022-03-11 10:47:17 UTC  
> Updated at: 2022-03-11 20:41:44 UTC  
> Url: https://github.com/boostorg/math/issues/775  

From this [write up](https://bitbashing.io/comparing-floats.html) float distance could be several times better. I can poke around when implementing `constexpr std::nexttoward` as that is the last outlier in ccmath from [this proposal. ](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p0533r8.pdf)

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-03-11 16:42:23 UTC  
> Url: https://github.com/boostorg/math/issues/775#issuecomment-1065287732  

Yeah, I think getting it to work quickly with multiprecision is the tricky part.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-03-11 17:12:03 UTC  
> Url: https://github.com/boostorg/math/issues/775#issuecomment-1065314050  

>Yeah, I think getting it to work quickly with multiprecision is the tricky part.  
  
We would need the existing implementation for all non-builtin non-IEEE floats IMO.  
  
But it's not just those, I suspect it's much harder than that blog post suggests once you handle all the corner cases correctly.  I do remember that that code was a bitch to get correct.  
  
We should have fairly good tests though, so if Matt wants to take a shot at the faster version by all means go for it :)

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-03-11 18:06:02 UTC  
> Url: https://github.com/boostorg/math/issues/775#issuecomment-1065356170  

Oh don't forget the `has_denorm`! The trick doesn't work if the floating point unit doesn't support denorms.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-03-11 18:23:55 UTC  
> Url: https://github.com/boostorg/math/issues/775#issuecomment-1065370278  

> Oh don't forget the has_denorm! The trick doesn't work if the floating point unit doesn't support denorms.  
  
It's worse, behaviour can vary at runtime: https://www.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/compiler-reference/floating-point-operations/understanding-floating-point-operations/setting-the-ftz-and-daz-flags.html

---

## Comment 5

> Username: NAThompson  
> Created at: 2022-03-11 20:41:44 UTC  
> Url: https://github.com/boostorg/math/issues/775#issuecomment-1065507178  

eww; wouldn't that require query of the cpuid instruction on every call?
