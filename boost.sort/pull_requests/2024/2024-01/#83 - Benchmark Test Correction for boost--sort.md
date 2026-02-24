# #83 Benchmark Test Correction for boost::sort [Open]

> Username: SebastianMenzelSAP  
> Created at: 2024-01-22 12:51:59 UTC  
> Updated at: 2024-01-25 00:31:45 UTC  
> Url: https://github.com/boostorg/sort/pull/83  

In reviewing the benchmark_numbers.cpp file, I noticed that the functions Generator_sorted_middle and Generator_reversed_sorted_middle have identical definitions. Additionally, certain parts of these functions' code lack coherence. I am proposing a resolution for these issues.  
  
Problems Identified:  
  
1. The method Generator_reversed_sorted_middle currently sorts the elements after swapping them, contradicting the intention of a reversed sort ( l. 246 - 247 ). In Generator_reversed_sorted_middle the swapping also happens, with no use afterwards ( l. 188 - 189).  
  
2. The Generator_sorted_middle and Generator_reversed_sorted_middle functions generate more elements compared to other methods. For instance, the Generator_sorted_middle function generates 100200000 elements when running sorted + 0.1% end, while other functions generate only 100100000. This unequal element generation inhibits fair comparison between different algorithms.  
  
3. The algorithm execution leads to an out of bound access at lines 199 and 257.  
  
Proposed Solution:  
I have made adjustments to address these issues. The changes lead to more consistent and accurate benchmark tests between sorting algorithms. I look forward to feedback on my proposed revisions.

---
