# #1214 Fix long compilation times for epsg [Closed]

> Username: tinko92  
> Created at: 2023-11-11 23:22:36 UTC  
> Updated at: 2025-05-15 17:09:27 UTC  
> Closed at: 2025-05-13 22:42:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1214  

This PR is intended to fix #1006 . @vissarion , I saw your comment about GCC 12.1 being an outlier at the time but unfortunately it now occurs with GCC 13.2.1 and GCC 12.3.0 too. I also get long (40s-60s on an Intel i7-12800H) compilation times with Clang 16 (haven't tried other versions) if I use any kind of optimization (-O{1,2,3}), which seems unreasonable for a <10k line header that contains only a list of static data. I didn't record the exact numbers but I also saw excessive compilation times for this header with MSVC.  
  
This PR changes the code_element type to a statically sized object without heap allocations (similar to the one proposed by @sigbjorn in #1006 ). Since the keys are all known at compile-time, the binary search can also be replaced with perfect hashing.  
  
A test for the equality of the parameters-arrays in this PR and the one currently in boost is implemented here: https://gist.github.com/tinko92/3b55c5288b8ff229e541cfa76257f892  
  
The code that was used to generate the code in this PR from the old one is found here (uses std::format and magic_enum):  
https://gist.github.com/tinko92/869d610859e53a2a1906b7ae19b67e8c  
  
The perfect hashing functions are generated using https://github.com/rurban/nbperf and then slightly edited (formatting, constexpr).  
  
# Advantages  
- 600x faster compilation with affected GCC versions, 50x faster compilation with Clang if optimizations are enabled, generally <1s compilation times across all tested configurations.  
- Worst-case O(1) lookup rather than O(log(n)).  
- No more ~4k heap allocations when epsg_to_parameters is first called.  
- Functions in the binary are a lot smaller, all the data is in .rodata.  
- Overall compiled binary size for the example in issue #1006 is 560kB (~40%) smaller with Clang 16 and -O2. For equivalent examples with esri.hpp and iau2000.hpp, the size reductions are 100kB (~53%) and 332kB (~64%) respectively. Greater savings with GCC.  
  
# Disadvantages  
- Adding/removing transformations requires rehashing. This could be avoided by reverting to binary search at the cost of O(1) lookup.  
- Code is less readable and longer than before (but imho still reasonably readable).  
  
I was hoping, it would also lead to smaller binaries when the code is known at compile-time (At least Clang seems to be able in principle to optimize away this kind of hashing with static keys: https://godbolt.org/z/Kq66anToo) but that did not work out unfortunately, maybe I am missing something to make that work.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-05-13 20:28:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1214#issuecomment-2877863455  

@tinko92 I didn't react at that time - because I wanted to think about it (and later forgot).  
I'm not in favour of this. It adds too much complexity. Can we close it?

---

## Comment 2

> Username: tinko92  
> Created_at: 2025-05-13 22:42:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1214#issuecomment-2878120940  

> @tinko92 I didn't react at that time - because I wanted to think about it (and later forgot). I'm not in favour of this. It adds too much complexity. Can we close it?  
  
I understand, yes it can be closed.  
  
If the main complexity objection is with the perfect hashing, maybe a different PR that just replaces the current chaining (which I think leads to the long compile times) with a simpler initialisation approach can be considered to address #1006 ? I am currently very time-constrained, but I will try to look into this again in summer.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2025-05-15 17:09:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1214#issuecomment-2884521160  

Thank you! And thanks for the ref to #1006 again. It's listed as close because they found another solution - so we can verify the need and the workflow.

---
