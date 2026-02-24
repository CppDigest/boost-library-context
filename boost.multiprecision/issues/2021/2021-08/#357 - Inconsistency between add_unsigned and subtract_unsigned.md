# #357 - Inconsistency between add_unsigned and subtract_unsigned [Closed]

> Username: NeillClift  
> Created at: 2021-08-19 14:51:32 UTC  
> Updated at: 2021-12-19 09:35:32 UTC  
> Closed at: 2021-12-19 09:35:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/357  

if (i == x && carry)  
      {  
         // We overflowed, need to add one more limb:  
         result.resize(x + 1, x + 1);  
         if (result.size() > x)  
            result.limbs()[x] = static_cast<limb_type>(1u);  
      }  
      else  
         std::copy(pa + i, pa + x, pr + i); - if I == x we do the copy. We also do the copy even if the output and input overlap  
  
subtract_unsigned:  
  
      // Any remaining digits are the same as those in pa:  
      if ((x != i) && (pa != pr)) ------ we do these checks here and avoid the copy  
         std_constexpr::copy(pa + i, pa + x, pr + i);  
  
I am seeing a lot of time being used by that general copy above in add_unsigned  when it can be avoided.  
  
There is a minor opportunity in the tail end carry propagation in both routines also:  
  
      for (; i < x && carry; ++i)  
         carry = ::boost::multiprecision::detail::addcarry_limb(carry, pa[i], 0, pr + i);  
  
Maybe this:  
      for (; i < x && carry; ++i)  
         carry = ::boost::multiprecision::detail::addcarry_limb(0, pa[i], 1, pr + i);  
  
Generates just an inc when I tried it. Don't expect propagation to be significant generaly.
