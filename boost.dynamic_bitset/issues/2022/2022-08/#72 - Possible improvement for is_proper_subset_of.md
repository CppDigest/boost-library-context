# #72 - Possible improvement for is_proper_subset_of [Closed]

> Username: rhalbersma  
> Created at: 2022-08-28 12:10:28 UTC  
> Updated at: 2025-08-18 15:40:52 UTC  
> Closed at: 2025-08-18 15:40:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/72  

The current implementation for the `is_proper_subset_of` can be slightly improved by breaking out of the loop after `proper` has been set to `true`. From that point forward, the remaining words only have to be checked for the subset-relation.   
  
If the `proper` part is on average found after half the number of currently used words, then in the remaining half of the words only half the number tests need to be done.  So the savings would on average amount to a quarter of the number of `if`-statements on random data.  
  
The code would look like:  
  
~~~  
template <typename Block, typename Allocator>  
bool dynamic_bitset<Block, Allocator>::  
is_proper_subset_of(const dynamic_bitset<Block, Allocator>& a) const  
{  
    assert(size() == a.size());  
    assert(num_blocks() == a.num_blocks());  
  
    size_type i = 0;  
    for (/* init-statement before loop */; i < num_blocks(); ++i) {  
        if (m_bits[i] & ~a.m_bits[i])  
            return false;   // not a subset at all  
        if (a.m_bits[i] & ~m_bits[i])  
            break;          // proper  
    }  
    if (i == num_blocks)  
        return false;       // not proper, because break-statement not hit  
    ++i;                    // the break-statement short-circuited the increment  
    for (/* re-use i from previous loop */; i < num_blocks(); ++i)  
        if (m_bits[i] & ~a.m_bits[i])  
            return false;   // not a subset  
    return true;  
}  
~~~  
  
If a PR along these lines would be welcome, then I'd be happy to submit one.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-07-29 17:49:39 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/72#issuecomment-3133480862  

Hi Rein,  
  
how are you doing? :-) Sorry for the late reply, but DynamicBitset was without a maintainer until a few weeks ago. I've taken a look at your implementation, and I'm a bit undecided. It's more complex than the current one, and, as you say, it just saves, on average, ¼ `num_blocks()` tests. I suspect most `dynamic_bitset`'s have 32-bit or 64-bit blocks, and thus a small number of blocks even if `size()` is in the range of thousands, so the saving would be pretty slim, and it might even turn out to be a pessimization. For example, on a 1000-bit bitset with 64-bit blocks, the saving would be 4 tests.
