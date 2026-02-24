# #563 - Syntax of casting in 1.83 [Closed]

> Username: ckormanyos  
> Created at: 2023-08-21 07:23:20 UTC  
> Updated at: 2023-08-22 05:48:05 UTC  
> Closed at: 2023-08-22 05:48:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563  

In 1.83 and maybe for a while I've found a syntax of casting issue that I'd like to address prior to 1.84.  
  
Issue:  
I'm not exactly sure how to repair the case (although I might have refactored the line myself to begin with). Anyway, could we review [this line](https://github.com/boostorg/multiprecision/blob/5f81a78cdb38ea35f98ef7a655d2535773c1261b/include/boost/multiprecision/cpp_int/divide.hpp#L555) together John (@jzmaddock)?  
  
Adding `limb_type` to `limb_type` to get a `double_limb_type` was flagged by one of my many syntax checkers. And I agree with the flag.  
  
I came up with the line below. Thoughts?  
  
```cpp  
   const double_limb_type two_n_mod = static_cast<double_limb_type>(static_cast<double_limb_type>(1u) + static_cast<double_limb_type>(static_cast<limb_type>(~static_cast<limb_type>(0u) - mod) % mod));  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-08-21 07:56:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563#issuecomment-1685834621  

Or do we need to cast the bitwise not of zero like below?  
  
```cpp  
   const double_limb_type two_n_mod = static_cast<double_limb_type>(static_cast<double_limb_type>(1u) + static_cast<double_limb_type>(static_cast<double_limb_type>(~static_cast<double_limb_type>(0u) - mod) % mod));  
```  
  
Or does it just _not_ _matter_ since the initialization is only in the lower-limb of the double-limb type anyway?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-08-21 08:39:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563#issuecomment-1685900645  

In terms of correctness, mod must be >0 so ` (~static_cast<limb_type>(0u) - mod)` is less than the largest representable value, so adding one (after the mod which can only reduce the value further or leave it the same) is fine and cannot overflow.  
  
But to fix the lint checkers, this should be fine:  `const double_limb_type two_n_mod = static_cast<double_limb_type>(1u) + static_cast<double_limb_type>((~static_cast<limb_type>(0u) - mod) % mod);`

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-08-21 09:10:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563#issuecomment-1685947100  

> adding one (after the mod which can only reduce the value further or leave it the same) is fine and cannot overflow  
  
Ah, yes. Thanks John  
  
If it's ok then, I'll _hammer_ in one different variation to add `limb + limb = limb`, then cast to `double_limb`. Then I'll PR it after clearing up another PR in the pipeline.  
  
```cpp  
   const double_limb_type two_n_mod =  
      static_cast<double_limb_type>  
      (  
         static_cast<limb_type>(1u + static_cast<limb_type>(static_cast<limb_type>(~static_cast<limb_type>(0u) - mod) % mod))  
      );  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-08-21 09:17:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563#issuecomment-1685957050  

I suspect that will still trip up the lint tools, as I assume they're warning on the +1 addition?

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-08-21 09:43:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/563#issuecomment-1685999982  

> suspect that will still trip up the lint tools, as I assume they're warning on the +1 addition?  
  
Ugghhh, yes, it was. Great insight.  
  
I finally opted (am wanting to opt) for this:  
  
```cpp  
   const double_limb_type two_n_mod =  
      static_cast<double_limb_type>  
      (  
         static_cast<double_limb_type>(1u) + static_cast<limb_type>(static_cast<limb_type>(~static_cast<limb_type>(0u) - mod) % mod)  
      );  
```  
  
Much ado (from my side) about a line, I know... But, hey, at least I'm not goinng on about `auto` type deduction (yet), ...
