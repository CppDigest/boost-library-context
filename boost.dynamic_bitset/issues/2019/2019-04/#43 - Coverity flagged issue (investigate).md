# #43 - Coverity flagged issue (investigate) [Closed]

> Username: jeking3  
> Created at: 2019-04-26 19:17:12 UTC  
> Updated at: 2019-06-07 17:56:55 UTC  
> Closed at: 2019-06-07 17:56:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/43  

In https://github.com/boostorg/dynamic_bitset/blob/develop/include/boost/dynamic_bitset/dynamic_bitset.hpp#L1319:  
  
result_type result = 0;  
    	3. assignment: Assigning: i = 0UL.  
    	4. Condition i <= last_block, taking true branch.  
    	6. incr: Incrementing i. The value of i is now 1.  
    	7. Condition i <= last_block, taking true branch.  
1319  for (size_type i = 0; i <= last_block; ++i) {  
    	8. assignment: Assigning: offset = i * 64UL. The value of offset is now 64.  
1320    const size_type offset = i * bits_per_block;  
    	  
CID 120258 (#1-3 of 3): Bad bit shift operation (BAD_SHIFT)  
9. large_shift: In expression static_cast<boost::dynamic_bitset::to_ulong() const::result_type (instance 42)>(this->m_bits[i]) << offset, left shifting by more than 63 bits has undefined behavior. The shift amount, offset, is 64.  
1321    result |= (static_cast<result_type>(m_bits[i]) << offset);  
    	5. Jumping back to the beginning of the loop.  
1322  }  
1323  
1324  return result;

---

## Comment 1

> Username: jeking3  
> Created at: 2019-06-07 17:56:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/43#issuecomment-499980719  

This was determined to be a false positive.    
There's an assertion right above it that would fail if offset was >= 64
