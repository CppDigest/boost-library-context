# #1203 - Review 2:  Simplify numeric_limits [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 17:49:14 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-06 13:28:16 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203  

- The specializations of `std::numeric_limits` use conditional compilation to select between `class` and `struct`, and to specify `public:` access:  
  
  template <>  
  #ifdef _MSC_VER  
  class numeric_limits<boost::decimal::decimal64_t>  
  #else  
  struct numeric_limits<boost::decimal::decimal64_t>  
  #endif  
  {  
  #ifdef _MSC_VER  
      public:  
  #endif  
  
I think just using `class` and `public:` across all compilers is simpler and effective.  
  
Comment: I think this is what we do in multiprecision and it seems not to cause issues.  
  
The has_demorm member is going to cause no end of issues going forward - both for you and multiprecision, there appears to be no good solution to that one.  
  
And finally, I believe your numeric_limits specializations need out-of-line definitions for their members, as it is still possible to take their address either deliberately or inadvertently.  Since these are full specializations, this is going to cause no end of pain since you can't place those in headers "as is".  One option might be to derive the specialization from a template with a dummy template parameter and add the members to that.  And yes that sucks!  C++17 fixes this of course with inline static variables.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-11-05 18:49:52 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3492811348  

> I think just using `class` and `public:` across all compilers is simpler and effective.  
  
I might have had something to do with this mixup. It was for some very old compilers. I can go with `class` and `public:` but we might have to disable some ancient arkane warning somewhere, which would then be no problem.

---

## Comment 2

> Username: mborland  
> Created at: 2025-11-06 07:39:47 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3495550302  

If we go with just `class` the old clangs complain it should be a `struct` so we'd have to wrap with their flavor of warning suppression. Perhaps that's nominally less ugly? The current situation is the least lines of warning wrapping

---

## Comment 3

> Username: mborland  
> Created at: 2025-11-06 07:42:42 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3495570178  

As an aside I'm becoming more inclined to bump this lib up to C++17 prior to initial release. Our support is as so:  
  
GCC 7 and later  
Clang 6 and later  
Visual Studio 2019 and later  
  
These all have nearly complete support for C++17, and definitely for the parts we use. This is our lower bound on compilers not because of language support, but older compilers just couldn't get the tests to build.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-11-06 08:34:39 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3495867447  

> As an aside I'm becoming more inclined to bump this lib up to C++17 prior to initial release. Our support is as so  
  
Hmmm... I actually might not suggest this Matt. We have C++14 support and at the moment, nobody is complaining about that.  
  
I'm not actually sure if `decimal` gains any value by _dropping_ C++14 support. The thing is close to finished anyway. I would not see the real added value of jumping up to 17?

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-11-06 08:36:16 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3495873498  

Also, I don't know if others got to this point, but I'm a 14 or 20-plus guy.

---

## Comment 6

> Username: mborland  
> Created at: 2025-11-06 08:58:26 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3495972629  

> Also, I don't know if others got to this point, but I'm a 14 or 20-plus guy.  
  
For boost I think C++(current year - 10) is a safe bet. Pretty soon here 17 will be the default language for the default compiler on the major linux distributions (Ubuntu and RedHat)

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-11-06 10:00:20 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3496289826  

> If we go with just `class` the old clangs complain it should be a `struct`  
  
Indeed. Here is the logic that I have used in other projects for this.  
  
```  
  #if (defined(__clang__) && (__clang_major__ <= 9))  
    // use struct for numeric_limits  
  #else  
    // use class and public: for numeric_limits  
  #endif  
```

---

## Comment 8

> Username: mborland  
> Created at: 2025-11-06 10:11:41 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3496347630  

I went with:  
  
```  
namespace std {  
  
#ifdef __clang__  
#  pragma clang diagnostic push  
#  pragma clang diagnostic ignored "-Wmismatched-tags"  
#endif  
  
template <>  
class numeric_limits<boost::decimal::decimal_fast128_t> :  
    public boost::decimal::detail::numeric_limits_impl128f<true> {};  
  
#ifdef __clang__  
#  pragma clang diagnostic pop  
#endif  
  
} // namespace std  
```  
  
If we change around what is being a class and what is being a struct I assume people will still complain.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2025-11-06 13:26:08 UTC  
> Url: https://github.com/boostorg/decimal/issues/1203#issuecomment-3497262787  

Looks like you're dammed either way on the class/struct issue: just go with whatever is easiest (including leaving it alone).
