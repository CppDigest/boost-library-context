# #703 - Findings cpp_fp_double_backend [Open]

> Username: ckormanyos  
> Created at: 2025-06-28 19:07:16 UTC  
> Updated at: 2025-09-05 05:40:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/703  

Here are some open tasks for further quality enhancement of the new `cpp_double_fp_backend<T>` template  
  
- [ ] #728  
- [x] Better infinity catches add/sub. Fixed by 715e0b461e4c4f9771483c5b1bd8b900718779e8.  
- [ ] Catch `(max)()` and `lowest()` in `eval_convert_to`-floating-point.  
- [ ] Consider `numeric_limits` values particularly `(max)()` and `epsilon()`. Are there even better values for these?  
- [x] #727  
- [x] Edge cases of `pow(x, a)` function, in other words $x^a$, for $x{\in}{\mathbb{R}}$, $a{\notin}{\mathbb{R}}$. See also #722  
- [x] #722  
- [x] When testing, it was discovered `cpp_dec_float` does not compile with `complex_adaptor`. Fixed by 1ac05b1832d7c71812ff841d4d1624fe8d511d07  
- [ ] The relative errors when testing data from the table with `cpp_double_fp` in `test_pow.cpp` subjectively seem a bit high. Why?  
- [ ] Investigate and address the _TBD_ comments in `test_various_edges_more.cpp`  
  
Notes:  
  
  
- The new `cpp_double_fp_backend` type is composed of two limbs. At the present time, add/sub/mul/div always do full 2-by-2 limb operations. This issue investigates odd double-limb, single-limb operations like the double-fp type being multiplied by a single fp-type having the same width as its constituents.
