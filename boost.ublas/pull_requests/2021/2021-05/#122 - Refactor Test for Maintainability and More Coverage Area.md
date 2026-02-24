# #122 Refactor Test for Maintainability and More Coverage Area. [Merged]

> Username: amitsingh19975  
> Created at: 2021-05-31 08:11:23 UTC  
> Updated at: 2022-02-08 14:51:51 UTC  
> Merged at: 2022-02-03 17:25:09 UTC  
> Closed at: 2022-02-03 17:25:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/122  

The tests have been revamped, which improved the maintainability, and easier to reason about the code. The tests have also been decoupled from monolithic tests into smaller tests and added the missing tests for different tensors and extents with keeping readability in mind and intent of the tests.

---

## Review 1 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:25:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/122#pullrequestreview-672076515  

Reports by clang tidy

📁 test/tensor/test_strides.cpp

```diff
 198 |+         check(n213, {1ul, 2ul,  2ul});
 199 |+         check(n321, {1ul, 3ul,  6ul});
 200 |+         check(n432, {1ul, 4ul, 12ul});
```

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:25:06 UTC  
> Updated_at: 2021-05-31 08:25:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r642308643  

12ul is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
check(n432, {1ul, 4ul, 12ul});  
                               ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_strides.cpp

```diff
 203 |+     BOOST_TEST_CONTEXT("[Dynamic Strides(First Order)] rank(4) dynamic strides"){
 204 |+         check(n1111, {1ul, 1ul, 1ul, 1ul});
 205 |+         check(n4231, {1ul, 4ul, 8ul, 24ul});
```

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:25:06 UTC  
> Updated_at: 2021-05-31 08:25:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r642308647  

24ul is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
check(n4231, {1ul, 4ul, 8ul, 24ul});  
                                     ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_strides.cpp

```diff
 242 |+         check(n213, {1ul, 2ul,  2ul});
 243 |+         check(n321, {1ul, 3ul,  6ul});
 244 |+         check(n432, {1ul, 4ul, 12ul});
```

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:25:06 UTC  
> Updated_at: 2021-05-31 08:25:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r642308651  

12ul is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
check(n432, {1ul, 4ul, 12ul});  
                               ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_strides.cpp

```diff
 247 |+     BOOST_TEST_CONTEXT("[Static Rank Strides(First Order)] static rank(4) dynamic strides"){
 248 |+         check(n1111, {1ul, 1ul, 1ul, 1ul});
 249 |+         check(n4231, {1ul, 4ul, 8ul, 24ul});
```

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:25:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r642308652  

24ul is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
check(n4231, {1ul, 4ul, 8ul, 24ul});  
                                     ^  
```  
Reported as warning by clang-tidy.


---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-05-31 08:43:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#issuecomment-851326254  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Review 3 [Approved]

> Username: bassoy  
> Created_at: 2022-01-29 18:46:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/122#pullrequestreview-866981042  

In general, this pull request contains very necessary and helpful changes to the code base. The tests now contain BOOST_TEST_DECORATORS  and BOOST_TEST_CONTEXT which provide valuable information for debugging. Most importantly, functions are tested with different tensor types using multiple tensor instances and extents.  
   
I would advise to integrate the following corrections & additions   
- Give better function names for `check<1-4>` within the unit tests. See comment in [`test_algorithm_transform.cpp`](https://github.com/boostorg/ublas/pull/122/files#diff-138412a6515a788481bd9bc28deafd0946a6c0ed7fb78ba130d465d508b411da)  
- Define `check<1-4>` functions in unit tests recursively so that only one function is sufficient.   
- Split `test/tensor/fixture_utility.hpp` in two or more files.  
- Rename `test/tensor/functions/test_functions_vector.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_vector.cpp`  
- Rename `test/tensor/functions/test_functions_matrix.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_matrix.cpp`  
- Rename `test/tensor/functions/test_functions_tensor.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_tensor.cpp`  
- Rename `test/tensor/functions/test_functions_tensor_permutation.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_tensor_with_permutation.cpp`

📁 test/tensor/algorithm/test_algorithm_transform.cpp

```diff
  42 |+ 
  43 |+ template<typename FnType>
  44 |+ constexpr auto check3(auto const& l, auto const& wl, auto const& r, auto const& wr, auto const& n, FnType&& pred) noexcept{
```

> Username: bassoy  
> Created_at: 2022-01-29 16:17:58 UTC  
> Updated_at: 2022-01-29 18:46:05 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795070549  

I am sure that we can define those recursively so that `check<1-4>` can be expressed with a single `check function`.

> Username: amitsingh19975  
> Created_at: 2022-01-30 11:24:34 UTC  
> Updated_at: 2022-01-30 11:25:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795166627  

We can do it, but I don't know about the runtime of tests because raw loops are faster than recursions. That's what functional programming does, which does not support for-loops.

> Username: bassoy  
> Created_at: 2022-01-30 12:24:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795174043  

I feel that this is some type of premature optimization. I suggest in future to create a recursive one and see if that really has an impact on the test.

> Username: amitsingh19975  
> Created_at: 2022-01-30 12:32:19 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795175290  

It was easier to write and use. That's why I did this way at that time.


📁 test/tensor/functions/test_functions_outer.cpp

```diff
  80 |+         static constexpr auto a_rank = a.rank();
  81 |+         
  82 |+         if constexpr(a_rank > 1ul){
```

> Username: bassoy  
> Created_at: 2022-01-29 17:44:11 UTC  
> Updated_at: 2022-01-29 18:46:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795078467  

Early return is better.

> Username: amitsingh19975  
> Created_at: 2022-01-30 11:26:43 UTC  
> Updated_at: 2022-01-30 11:38:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795166872  

It won't be an issue because the branch will be removed from the assembly. Hence, it will be an empty function.

> Username: bassoy  
> Created_at: 2022-01-30 12:16:46 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795172851  

Agree from the performance perspective. I would like to decrease the indention.

> Username: amitsingh19975  
> Created_at: 2022-01-30 12:30:53 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795175073  

The problem will arise when the tensor is static. The `constexpr` prevents compilation errors that can the compilation impossible because the compiler sees `constexpr` and say "oh, the condition does not match. Therefore, I don't need to look inside the branch, and it ignores all the error that is produced inside the branch". Consider this  
  
```cpp  
template<std::size_t N>  
constexpr auto test_helper() noexcept{ static_assert(N > 2); };  
  
template<std::size_t N>  
constexpr auto test() noexcept{   
   if constexpr( N <= 2 ) return;  
   test_helper<N>();  
};  
  
tuple<index_t<0>, index_t<1>, index_t<2>, index_t<3>> t;  
// for_each_fixture: it cannot skip indices.  
for_each_fixture(t, [](...){}); // static error  
```  
The example is not that good, but it does the job.

---

📁 test/tensor/functions/test_functions_outer.cpp

```diff
  86 |+                 constexpr auto b_rank = b.rank();
  87 |+                 
  88 |+                 if constexpr(b_rank > 1ul){
```

> Username: bassoy  
> Created_at: 2022-01-29 17:44:38 UTC  
> Updated_at: 2022-01-29 18:46:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795078491  

Early return is better.

> Username: amitsingh19975  
> Created_at: 2022-01-30 11:27:28 UTC  
> Updated_at: 2022-01-30 11:27:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795166963  

same reason as the [issue](https://github.com/boostorg/ublas/pull/122#discussion_r795166872).


📁 test/tensor/tensor/test_tensor_assignment_operator.cpp

```diff
  75 |+ 
  76 |+     BOOST_TEST_CONTEXT("[Dynamic Tensor Copy Assignment Operator] copying tensor"){
  77 |+         auto o = fixture_type::t32;
```

> Username: bassoy  
> Created_at: 2022-01-29 18:28:53 UTC  
> Updated_at: 2022-01-29 18:46:13 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795081598  

Why is only one tensor instance taken considered?

> Username: amitsingh19975  
> Created_at: 2022-01-30 11:36:27 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#discussion_r795167958  

There are two reasons:  
- More tests -> slower runtime and the GitHub actions fails due to overuse of RAM.  
- Because it constructs a new tensor and then it swaps them. Therefore, it has less surface area for testing, and we are already intensely tested constructors which, in turn, makes the tests redundant or unnecessary.


---

## Review 4 [Approved]

> Username: bassoy  
> Created_at: 2022-01-29 18:47:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/122#pullrequestreview-866997796  

In general, this pull request contains very necessary and helpful changes to the code base. The tests now contain BOOST_TEST_DECORATORS  and BOOST_TEST_CONTEXT which provide valuable information for debugging. Most importantly, functions are tested with different tensor types using multiple tensor instances and extents.  
   
I would advise to integrate the following corrections & additions   
- Give better function names for `check<1-4>` within the unit tests. See comment in [`test_algorithm_transform.cpp`](https://github.com/boostorg/ublas/pull/122/files#diff-138412a6515a788481bd9bc28deafd0946a6c0ed7fb78ba130d465d508b411da)  
- Define `check<1-4>` functions in unit tests recursively so that only one function is sufficient.   
- Split `test/tensor/fixture_utility.hpp` in two or more files.  
- Rename `test/tensor/functions/test_functions_vector.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_vector.cpp`  
- Rename `test/tensor/functions/test_functions_matrix.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_matrix.cpp`  
- Rename `test/tensor/functions/test_functions_tensor.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_tensor.cpp`  
- Rename `test/tensor/functions/test_functions_tensor_permutation.cpp` in `test/tensor/functions/test_functions_prod_tensor_times_tensor_with_permutation.cpp`

---

## Comment 5

> Username: amitsingh19975  
> Created_at: 2022-01-29 20:03:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/122#issuecomment-1024977370  

Thanks for pointing out changes, and they will be fixed in the next pull request.

---
