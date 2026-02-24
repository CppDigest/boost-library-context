# #88 - Boost 1.83.0: clang-tidy finds UB shift in dynamic_bitset's to_ulong [Closed]

> Username: Bouncner  
> Created at: 2025-06-15 19:07:37 UTC  
> Updated at: 2025-08-04 10:21:47 UTC  
> Closed at: 2025-08-04 10:21:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/88  

Hi, we are using dynamic bitsets.  
With our current move to update our CI pipeline, we are using clang-tidy 20 (I think it was already an issue in 18 and 19).  
  
We are using a dynamic bitset with an asserted maximum of 63 bits.  
Clang tidy complains with the following issue:  
```  
/usr/include/boost/dynamic_bitset/dynamic_bitset.hpp:1347:52: note: The result of left shift is undefined because the right operand '64' is not smaller than 64, the capacity of 'result_type'  
 1347 |     result |= (static_cast<result_type>(m_bits[i]) << offset);  
```  
  
For us, I think it is certainly a false positive as we never reach the second loop iteration in line 1346. But for other cases, I am curious if clang-tidy is right.  
Debugging the code, I see that `const size_type offset = i * bits_per_block;` is 64 due to `1 * 64` in  the second iteration. That means `result |= (static_cast<result_type>(m_bits[i]) << offset);` does shift by 64 (m_bit's elements are `unsigned long`).

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-07-07 10:31:24 UTC  
> Updated at: 2025-07-07 10:36:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/88#issuecomment-3044380089  

Hi, thanks for reporting this. How can the shift amount be too large?  
  
The following always holds:  
  
`offset <= last_block * bits_per_block <= maximum_size - 1 < maximum_size <= ulong_width`  
  
That is, `offset` is always less than the width of an `unsigned long` (as also checked by the assert in the code). And `m_bits[ i ]` is cast to `unsigned long` before the shift.

---

## Comment 2

> Username: Bouncner  
> Created at: 2025-07-29 15:41:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/88#issuecomment-3133083652  

Sorry, I haven't found the time to fully dig into the bitset.  
Do I understand you correctly that this is a false positive of clang tidy? No matter how large the bitset is?

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-07-29 16:20:01 UTC  
> Updated at: 2025-07-29 16:23:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/88#issuecomment-3133215096  

Yes, I believe that's a false positive no matter how large the bitset is. If you look at the function implementation, you'll see the variables `offset`, `last_block`, `maximum_size` and it's easy to prove that they obey the inequality I gave above (implying `offset < ulong_width`). So, we are shifting `static_cast< result_type >( m_bits[ i ] )`, which is an `unsigned long`, by `offset`, which is smaller than the width of an `unsigned long`. This is also checked by the assert in the same function (which checks the maximum value of `offset`).
