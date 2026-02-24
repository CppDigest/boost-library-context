# #98 merge_blocks: const pos_index1, pos_index2 and pos_index3 to resolve cppcheck false alarm [Merged]

> Username: nigels-com  
> Created at: 2025-09-04 01:02:15 UTC  
> Updated at: 2025-09-13 02:10:11 UTC  
> Merged at: 2025-09-13 02:10:11 UTC  
> Closed at: 2025-09-13 02:10:11 UTC  
> Url: https://github.com/boostorg/sort/pull/98  

[cppcheck](https://cppcheck.sourceforge.io/) complains about a potential _Out of bounds access_ in merge_blocks implementation:  
  
```  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/blk_detail/merge_blocks.hpp:207:19: error: Out of bounds access in expression 'vpos2.back()' because 'vpos2' is empty. [containerOutOfBounds]  
    if (vpos2.back().pos() == (bk.nblock - 1)  
                  ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/blk_detail/merge_blocks.hpp:200:35: note: Assuming condition is false  
    for (size_t i = pos_index2; i < pos_index3; ++i)  
                                  ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/blk_detail/merge_blocks.hpp:207:19: note: Access out of bounds  
    if (vpos2.back().pos() == (bk.nblock - 1)  
                  ^  
```  
  
However.  Changing the three index parameters to `const` seems sufficient to resolve the cppcheck (false) error.  
Seems like a low-risk and agreeable workaround.

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-04 03:10:12 UTC  
> Url: https://github.com/boostorg/sort/pull/98#issuecomment-3251618986  

MS Copilot said:  
  
I don't have write access to directly attach a review comment to your pull request. However, here’s the review comment you can copy and paste into your PR for convenience:  
  
---  
  
**Review Summary:**  
  
- The PR addresses a cppcheck warning about a potential out-of-bounds access in `merge_blocks.hpp`. The warning appears to be a false positive.  
- Marking the index parameters (`pos_index1`, `pos_index2`, and `pos_index3`) as `const` is a low-risk, code-quality improvement, clarifying intent and aiding static analysis tools.  
- The change does not affect logic or interfaces and requires no new tests.  
- The PR description is clear and well-justified.  
- Suggestion: Consider mentioning in a code comment or commit message that this is specifically to appease static analysis for future maintainers.  
  
**Conclusion:**    
This is a safe and well-motivated change. I recommend merging after confirming CI passes.

---
