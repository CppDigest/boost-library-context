# #7 Feature/longest increasing subsequence [Open]

> Username: mkurdej  
> Created at: 2014-08-17 16:49:10 UTC  
> Updated at: 2018-03-27 11:11:32 UTC  
> Url: https://github.com/boostorg/algorithm/pull/7  

A new algorithm: longest increasing subsequence that permits as well to find the longest decreasing/non-increasing/non-decreasing or differently ordered subsequence by defining comparison predicate. I have added tests for it using iterators, ranges, pointers and object's `operator()`.  
  
There are 2 functionalities actually: computing length (function `longest_increasing_subsequence_length` or method `longest_increasing_subsequence::compute_length`) and retrieving the subsequence (function `longest_increasing_subsequence_search` or `longest_increasing_subsequence::operator()`).  
There is also a creator function `make_longest_increasing_subsequence`.  
  
There are 3 possibilities to retrieve the subsequence:  
- through an output iterator as an argument  
- returning a vector of values  
- returning a vector of iterators (useful for big objects when there is no need for copy)  
  The two latter variants are chosen by giving a corresponding tag as an argument.  
  
Documentation will come soon.

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2016-10-10 21:06:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/7#issuecomment-252746364  

@mclow what do you think about this pull request? I think, these functions are very-very useful. We should add it to Boost.Algorithm.

---

## Comment 2

> Username: Morwenn  
> Created_at: 2018-03-27 09:47:02 UTC  
> Updated_at: 2018-03-27 09:48:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/7#issuecomment-376463828  

I know this pull request is a bit old, but I had to compute the length longest non-decreasing subsequence of a sequence at some point (to compute the measure of presortedness *Rem*), which is notably one of the features proposed here, so I'm willing to share some thoughts about implementation details:  
* I used a modified patience sort algorithm to compute the length, which also runs in O(n log n) and O(n) extra memory, but my implementation actually uses extra memory equivalent to the size of the longest non-decreasing subsequence, and not to the size of the original sequence. While this is still O(n), it can use less memory on average.  
* I can compute the size of the longest non-decreasing subsequence for forward iterators without added cost compared to random-access iterators.  
  
Now I'm not sure whether these improvements are useful since they are limited to the computation of the *size* of a longest increasing subsequence (I haven't try to compute the subsequence itself), which is rather specific, but it can be done to make the current proposed implementation more powerful if needed :)

---

## Comment 3

> Username: mkurdej  
> Created_at: 2018-03-27 11:11:32 UTC  
> Url: https://github.com/boostorg/algorithm/pull/7#issuecomment-376486755  

@Morwenn, that looks like nice optimizations for `lis_length`. Especially space reduction in the average case seems very promising. Some people might be interested in lowering requirements to forward iterators too.  
  
Anyway, I don't really work on this kind of things now and I don't have much time to work on this PR, so I let anyone interested hijack the authorship of this PR and continue the work.

---
