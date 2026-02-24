# #276 reduce dec_float static init with constexpr constants [Closed]

> Username: ckormanyos  
> Created at: 2020-12-12 19:39:00 UTC  
> Updated at: 2021-02-05 18:46:19 UTC  
> Closed at: 2020-12-13 12:47:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276  

This draft PR investigates using selected features of C++11 in order to reduce the heavy load of static initialization associated with `cpp_dec_float`.  
  
Historically `cpp_dec_float` has rquired certain static instances of class-local constants representing such things as min/max value of the class, epsilon, common small values such as 0.5, 1, 2, etc.  
  
With some C++11 constructs (like `<initializer_list>`, `constexpr`) it might be possible to remove some or most of these static constant values. The run-time impact is expected to be performance-neutral. However, these changes reduce memory consumption (especially for higher digit counts) and significantly reduce the scope and complexity of the so-called static initialization that runs before the call to `main()`.  
  
Aside: Future potential use improveements of `boost::math::constants` might possibly be able to be derived from this kind of technology, but these are not in the scope of this draft PR at the moment.  
  
The checked in code handles nan, inf, max, eps.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-12-13 11:13:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/276#pullrequestreview-550909468  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
  72 | {
  73 |  private:
  74 |+    static BOOST_CONSTEXPR_OR_CONST boost::uint32_t pow10_maker(boost::uint32_t n)
```

> Username: jzmaddock  
> Created_at: 2020-12-13 11:13:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#discussion_r541902800  

Since from the next release onwards we're moving to C++11 or later, this could just be straight `constepxr` and we can drop the macros now?


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2020-12-13 11:24:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/276#pullrequestreview-550910307  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
 290 |-       init.do_nothing();
 291 |-       return val;
 303 |+       return cpp_dec_float(cpp_dec_float_NaN);
```

> Username: jzmaddock  
> Created_at: 2020-12-13 11:24:34 UTC  
> Updated_at: 2020-12-13 11:24:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#discussion_r541904652  

I wonder about this - I _think_ what you're really looking for here is `consteval`, but that's a C++20 feature :(    
  
I'm reasonably sure that the constructor here will be runtime evaluated (ie worst than before!) unless every *use* of the function is marked as `constexpr` which is error prone at best.  I think I'm right in saying that GCC will optimise this to a const-expression when used anyway, but it's not required to, and I'm fairly sure msvc does not do so.  
  
So I think we either make these constants member values:  
  
```  
static constexpr cpp_dec_float nan = cpp_dec_float(cpp_dec_float_nan);  
```  
  
Which still requires an out of line definition (until C++17 anyway).  Or we do something like:  
  
```  
static constexpr const cpp_dec_float& nan()  
{  
  static constexpr cpp_dec_float val(cpp_dec_float_nan);  
   return nan;  
}  
```


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-12-13 11:32:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#issuecomment-743994209  

Hi Chris, this is a good idea IMO, but some of the details will be tricky to sort out - see my detailed comments.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2020-12-13 12:41:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#issuecomment-744002161  

Thank you for reviewing this, John. Your review points seem very clear, detailed and correct.  
  
> I'm reasonably sure that the constructor here will be runtime evaluated (ie worst than before!)  
  
I was also considering this. In particular, the default behavior of  `cpp_dec_float` uses `std::array` to contain its value field. So the performance hit of returning (on the fly, even with move-semantics) of  `std::array` will be more significant than I might have assumed. This run-time impact will be reduced if dynamic allocation is used.  
  
Your intuition seems correct, as the overall performance will be either slightly worse or significantly worse.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-12-13 12:44:34 UTC  
> Updated_at: 2020-12-13 12:47:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#issuecomment-744002523  

> Which still requires an out of line definition (until C++17 anyway). Or we do something like:  
  
```  
static constexpr const cpp_dec_float& nan()  
{  
   static constexpr cpp_dec_float val(cpp_dec_float_nan);  
   return val;  
}  
```  
  
That is worth considering.  
  
I am actually in the process of putting some of this stuff _on the metal_ within the context of some other research. I have C++11 and C++17 compilers from a variety of suppliers all competing to melt-down the RAM and ROM footprint of mega-digit calculations (another derivative work).  
  
So I can aside from this draft-PR keep investigation along these lines.  
  
In summary on this draft PR.  
  
- The original concept is worse, not better.  
- I will continue to consider the constexpr versions sketched in this and John's original comment.  
- Since no immediate action is wise, this draft PR is being closed.  
  
Thanks, John.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2020-12-13 12:47:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/276#issuecomment-744002867  

See summary in comments above.

---
