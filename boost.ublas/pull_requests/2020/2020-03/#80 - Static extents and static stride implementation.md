# #80 Static extents and static stride implementation [Merged]

> Username: amitsingh19975  
> Created at: 2020-03-21 13:36:01 UTC  
> Updated at: 2020-05-31 14:16:32 UTC  
> Merged at: 2020-04-18 11:20:03 UTC  
> Closed at: 2020-04-18 11:20:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/80  



---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:37:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378940887  

📁 examples/tensor/tensor_construction.cpp

```diff
  51 |+     auto t9_s = t9.strides();
  52 |+ 
  53 |+     puts("\nt1: ");
```

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:37:00 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396017270  

out of curiosity: why do you use `puts()` here rather than `std::cout` ?

> Username: amitsingh19975  
> Created_at: 2020-03-21 19:16:28 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396020570  

I think [Jason Turner](https://www.youtube.com/watch?v=VZTVmKOXLVU) can explain it better than me. If you would still like to know I could try. `puts` adds new-line character at the end by itself so I was trying to save a few characters and more about it you can learn from `Jason Turner` talked about in detail.

> Username: stefanseefeld  
> Created_at: 2020-03-21 20:28:40 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396026498  

Well, if the saving of a few characters is the reason, I'd argue this is a bad idea. You are using two distinct APIs and I/O frameworks to generate output. And while the implementers of the C++ standard library try really hard to keep the C++ API compatible with the C API, it's not always possible. For example, consider someone wanting to hook into the iostreams to redirect (or filter) the output stream. He would only see the data sent through `std::cout`, but not `puts()`.  
In short, there are quite a number of ramifications to consider, and just to save a few key strokes  isn't worth it.

> Username: bassoy  
> Created_at: 2020-03-21 21:02:32 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396028883  

Agree with Stefan here.

> Username: bassoy  
> Created_at: 2020-03-22 13:18:24 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092692  

Resolve conversation as file is outdated.


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:40:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378941177  

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 259 |+ 
 260 |+ } // namespace boost::numeric::ublas
 261 |+ #endif
```

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:40:54 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396017552  

nit-pick: missing newline at eof.

> Username: amitsingh19975  
> Created_at: 2020-03-21 19:08:15 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396019984  

I will fix it.

> Username: bassoy  
> Created_at: 2020-03-22 13:18:43 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092737  

Resolving conversation as file is outdated.


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:45:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378941475  

📁 include/boost/numeric/ublas/tensor/fwd.hpp

```diff
  13 |+ #define _BOOST_NUMERIC_UBLAS_TENSOR_FWD_HPP_
  14 |+ 
  15 |+ #include <iostream>
```

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:45:26 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396017888  

This header shouldn't be needed.

> Username: amitsingh19975  
> Created_at: 2020-03-21 19:19:12 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396020833  

I will remove it.

> Username: bassoy  
> Created_at: 2020-03-22 13:19:00 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092757  

resolving conversation as file is outdated.


---

## Review 4 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:52:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378941985  

📁 test/tensor/Jamfile

```diff
  45 |           unit_test_framework ]
  46 |+     :
  47 |+     : <define>BOOST_UBLAS_TENSOR_TESTING
```

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:52:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396018431  

What is your plan here ? To re-enable these tests incrementally as you make progress on this PR ? Migrate them elsewhere ? Or disable them entirely ?

> Username: amitsingh19975  
> Created_at: 2020-03-21 19:07:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396019875  

I was trying to reduce the use of exceptions which is costly using asserts but for testing, I could not use asserts as they abort the program. Instead, I used the macro to define two different behavior, one for testing ( exception ) and another for assert. To switch between them I was using this macro. I'm still testing this.

> Username: amitsingh19975  
> Created_at: 2020-03-21 19:18:31 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396020757  

Tests were disabled because of MSVC. Which is giving some errors which I'm trying fix.

> Username: stefanseefeld  
> Created_at: 2020-03-21 20:29:26 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396026544  

OK, no worries. But please mark the PR as work-in-progress, so reviewers know what to expect.

> Username: bassoy  
> Created_at: 2020-03-21 21:04:36 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396029055  

> I was trying to reduce the use of exceptions which is costly using asserts but for testing, I could not use asserts as they abort the program.  
  
How are exceptions more costly?

> Username: bassoy  
> Created_at: 2020-03-22 13:19:27 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092791  

Resolving conversation as file is outdated.


---

## Review 5 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-21 18:54:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378942170  

I'm excited to see this PR !  
Could you provide some context, such as your plans as to migrating the API ? I see a number of tests being disabled. Does this mean this PR is work in progress, and you are re-enabling them incrementally as you make progress ?  
Thanks !

---

## Review 6 [Changes requested]

> Username: bassoy  
> Created_at: 2020-03-21 20:59:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-378927441  

📁 include/boost/numeric/ublas/tensor/algorithms.hpp

```diff
 252 | 	if(c == nullptr || a == nullptr)
 253 |- 		throw std::runtime_error("Error in boost::numeric::ublas::trans: Pointers shall not be null pointers.");
 253 |+ 		throw std::length_error("Error in boost::numeric::ublas::trans: Pointers shall not be null pointers.");
```

> Username: bassoy  
> Created_at: 2020-03-21 15:40:18 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396002594  

This should stay as `std::runtime_error` as `std::length_error` indicate sizing problems. Currently tensor mostly throws `std::runtime_error`. At a later stage `std::invalid_argument`might be perhaps more appropriate.

---

📁 include/boost/numeric/ublas/tensor/algorithms.hpp

```diff
 254 | 
 255 | 	if(na == nullptr)
 256 |- 		throw std::runtime_error("Error in boost::numeric::ublas::trans: Pointers shall not be null.");
```

> Username: bassoy  
> Created_at: 2020-03-21 15:50:02 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396003364  

Same as above.


📁 examples/tensor/tensor_construction.cpp

```diff
 139 |+         std::cout<<t9_s[i]<<' ';
 140 |+     }
 141 |+     std::cout<<'\n';
```

> Username: bassoy  
> Created_at: 2020-03-21 15:57:55 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396004029  

Please overload the `operator << ()` with `std::ostream` for the extents and strides.

---

📁 examples/tensor/tensor_construction.cpp

```diff
 146 |+     static_assert(std::is_same<typename decltype(t9)::array_type, std::array<float,24> >::value,"");
 147 |+     static_assert(detail::is_static_extents<typename decltype(t9)::extents_type >::value,"");
 148 |+     static_assert(detail::is_dynamic<typename decltype(t8)::extents_type >::value,"");
```

> Username: bassoy  
> Created_at: 2020-03-21 15:59:30 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396004147  

Please print out the boolean values.

> Username: bassoy  
> Created_at: 2020-03-22 13:17:00 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092537  

Seems outdated. Resolving conversation.

---

📁 examples/tensor/tensor_construction.cpp

```diff
  29 |+ 
  30 |+     auto t8 = tensor<float,dynamic_extents<>,first_order,std::vector<float>>{1,2,3,4};
  31 |+     auto t9 = tensor<float,static_extents<1,2,3,4>,first_order,std::array<float,24>>();
```

> Username: bassoy  
> Created_at: 2020-03-21 16:07:30 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396004698  

It would be good to put comments here for better understanding of the supported types.  
or if you like:  
  
```cpp  
using element_type = float;  
using tensor_dyn_extents_dyn_rank  = tensor<element_type>;  
using tensor_dyn_extents_dyn_rank2 = tensor<element_type,dynamic_extents<>>;  
  
using tensor_dyn_extents_stat_rank = tensor<etype,dynamic_extents<4>>;  
// ...  
```

> Username: bassoy  
> Created_at: 2020-03-22 13:17:29 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092583  

File Outdated. Resolving conversation.


📁 include/boost/numeric/ublas/tensor/detail/config.hpp

```diff
  16 |+ #include <cassert>
  17 |+ 
  18 |+ #if defined(__GNUC__) || ( defined( __has_attribute ) && __has_attribute(always_inline) )
```

> Username: bassoy  
> Created_at: 2020-03-21 16:11:14 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396005062  

Let us only use `inline`, leaving the decision to the compiler to inline or not.  
I am against this configuration file.

> Username: bassoy  
> Created_at: 2020-03-22 13:20:07 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396092846  

Resolving this conversation as file is outdated.


---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2020-03-22 12:50:28 UTC  
> Updated_at: 2020-04-14 19:26:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-602195381  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=h1) Report  
> Merging [#80](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/cf72d035f4f54c1fca3fa8882007a338c03b594b&el=desc) will **decrease** coverage by `0.94%`.  
> The diff coverage is `60.62%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/80/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #80      +/-   ##  
===========================================  
- Coverage    67.42%   66.47%   -0.95%       
===========================================  
  Files           61       29      -32       
  Lines         9233     1414    -7819       
  Branches      3000      544    -2456       
===========================================  
- Hits          6225      940    -5285       
+ Misses         872       70     -802       
+ Partials      2136      404    -1732       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/algorithms.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9hbGdvcml0aG1zLmhwcA==) | `53.50% <0.00%> (+7.01%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/expression.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9leHByZXNzaW9uLmhwcA==) | `83.33% <ø> (ø)` | |  
| [include/boost/numeric/ublas/tensor/multi\_index.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aV9pbmRleC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/numeric/ublas/tensor/tensor.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci90ZW5zb3IuaHBw) | `67.70% <36.36%> (-9.10%)` | :arrow_down: |  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `38.82% <38.82%> (-4.47%)` | :arrow_down: |  
| [.../numeric/ublas/tensor/detail/extents\_functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9kZXRhaWwvZXh0ZW50c19mdW5jdGlvbnMuaHBw) | `69.44% <69.44%> (ø)` | |  
| [include/boost/numeric/ublas/tensor/strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9zdHJpZGVzLmhwcA==) | `75.00% <75.00%> (-11.67%)` | :arrow_down: |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `65.00% <80.00%> (-0.42%)` | :arrow_down: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX3N0cmlkZXMuaHBw) | `81.92% <81.92%> (ø)` | |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX2V4dGVudHMuaHBw) | `82.95% <82.95%> (ø)` | |  
| ... and [64 more](https://codecov.io/gh/boostorg/ublas/pull/80/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=footer). Last update [cf72d03...e68819c](https://codecov.io/gh/boostorg/ublas/pull/80?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 8 [Changes requested]

> Username: bassoy  
> Created_at: 2020-03-22 16:02:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-379005682  

We need to discuss about some design choices you made as this will greatly impact the future design of ublas. There are two main issues I am concerned here.  
  
1. The implementation of static extents is solved using predefined values (-1,-2) for the non-type template parameters. I wish here a solution using type-traits.  
2. The tensor template implementation deals with static extents using `if constexpr`. Although I like this C++17, it is not a good design pattern in this case. I wish to have traits or partial template specialization here.

📁 include/boost/numeric/ublas/tensor/detail/config.hpp

```diff
  12 |+ #ifndef _BOOST_UBLAS_TENSOR_DETAIL_CONFIG_HPP_
  13 |+ #define _BOOST_UBLAS_TENSOR_DETAIL_CONFIG_HPP_
  14 |+ 
```

> Username: bassoy  
> Created_at: 2020-03-22 13:34:31 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396094327  

Please remove this configuration file as ublas becomes much harder to maintain and test. See the current ublas matrix flags and test setup which produces so many variants which all need to be tested.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:33:56 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402244438  

removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/config.hpp

```diff
  28 |+     #define TENSOR_ASSERT(e,comment) if (!e) { throw std::runtime_error( comment ); }
  29 |+     inline static constexpr bool TENSOR_ASSERT_NOEXCEPT = false;
  30 |+ #endif
```

> Username: bassoy  
> Created_at: 2020-03-22 13:38:04 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396094689  

A value should be tested either by an `assert` or an `exception` statement. They mean different things and are not substitutable.   
  
- only use asserts where you want to catch an internal programming error  
- otherwise try to use exceptions.   
  
For more detailed information  [explanation-microsoft](https://docs.microsoft.com/en-us/cpp/cpp/errors-and-exception-handling-modern-cpp?view=vs-2019) and [explanation-isocpp](https://isocpp.org/wiki/faq/exceptions)

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:34:25 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402244641  

removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/config.hpp

```diff
  42 |+ #else
  43 |+     #define BOOST_UBLAS_TENSOR_INLINE BOOST_UBLAS_INLINE
  44 |+ #endif
```

> Username: bassoy  
> Created_at: 2020-03-22 13:40:30 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396094911  

The speed you might gain by such statements will not justify the time for maintanance.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:34:48 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402244831  

removed all config file.


📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  61 |+ std::string to_string(T const &e) {
  62 |+   if (e.empty()) {
  63 |+     return "{}";
```

> Username: bassoy  
> Created_at: 2020-03-22 13:53:55 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396096389  

I recommend to use the square brackets  `[1,2,3,4]` please, as it is for the tensor. We can simply copy the output to octave, matlab and python (numpy) environment without rewriting.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:35:10 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402245004  

changed to square brackets.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  64 |+   };
  65 |+   std::string s = "{ ";
  66 |+   for (auto i = 0; i < e.size() - 1; i++) {
```

> Username: bassoy  
> Created_at: 2020-03-22 14:13:14 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396098240  

Please use the unsigned literal `u` for  `auto i = 0u; i < e.size()-1;++i` .

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:36:04 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402245476  

changed to `typename T::size_type(0)`

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  67 |+       s += std::to_string(e.at(i)) + ", ";
  68 |+   }
  69 |+   s += std::to_string(e.at(e.size() - 1)) + " }";
```

> Username: bassoy  
> Created_at: 2020-03-22 14:13:53 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396098319  

Please use `e.back()` to access the last element of `e`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:37:33 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402246163  

created the API `back` and changed to it and now rather than using `std::string` I'm using `std::stringstream`.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  81 |+     return false;
  82 |+   }
  83 |+   if constexpr (!detail::is_static_extents<ExtentsType>::value) {
```

> Username: bassoy  
> Created_at: 2020-03-22 14:20:37 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396098976  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:40:50 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402247741  

added the `begin` and `end` API and changed to std algorithms.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  86 |+     });
  87 |+   } else {
  88 |+     auto arr = e.to_array();
```

> Username: bassoy  
> Created_at: 2020-03-22 14:24:25 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396099391  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:40:57 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402247787  

added the `begin` and `end` API and changed to std algorithms.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 206 |+   if (e.size() <= 2) {
 207 |+     if constexpr (detail::is_static_extents<ExtentsType>::value) {
 208 |+       return e.to_dynamic_extents();
```

> Username: bassoy  
> Created_at: 2020-03-22 14:28:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396099801  

Why is here a conversion to dynamic extents? This is an unexpected behavior for the user.   
  
T squeeze funciton cannot be used for dynamic extents with static rank while it can be used for static extents with static rank without any conversion.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:44:21 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402249503  

created the compile-time squeeze and removed `to_dynamic_extents` from static_extents but static rank with dynamic extents cannot be used with it because of runtime constraints, we do not the how much in size it will decrease.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 215 |+   auto old_extent = e.base();
 216 |+ 
 217 |+   auto insert_iter =
```

> Username: bassoy  
> Created_at: 2020-03-22 14:31:05 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396100139  

Can't use just use the function `std::back_inserter` which instantiates the desired iterator?

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:45:14 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402249912  

changed the whole design and removed the `std::back_inserter`

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  41 |+              return a == typename ExtentsType::value_type(0);
  42 |+            });
  43 |+   }
```

> Username: bassoy  
> Created_at: 2020-03-22 15:02:14 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103433  

I would like to see compile time implementation of `none_of` for static extents instead of the copy operation.  
  
I suggest that you define recursive function for `none_of` , `all_of` and `any_of` and to use those in all the following functions.  
  
```cpp  
template<typename Pred, typename Arg, typename... Args>  
inline constexpr bool none_of(Pred pred, Arg arg, Args ... args)   
{  
    if constexpr (sizeof...(Args) == 0) return !pred(arg);  
    else return none_of(pred,args...) && !pred(arg);  
}  
```  
  
Of course the argument could be your static extents type instead.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:45:40 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250129  

added the `begin` and `end` API and changed to std algorithms.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 128 |+   auto equal_one = [](auto const &a) { return a == typename ExtentsType::value_type(1); };
 129 |+ 
 130 |+   if constexpr (!detail::is_static_extents<ExtentsType>::value) {
```

> Username: bassoy  
> Created_at: 2020-03-22 15:03:20 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103557  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:45:56 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250255  

added the `begin` and `end` API and changed to std algorithms and removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 156 |+   auto equal_one = [](auto const &a) { return a == typename ExtentsType::value_type(1); };
 157 |+ 
 158 |+   if constexpr (!detail::is_static_extents<ExtentsType>::value) {
```

> Username: bassoy  
> Created_at: 2020-03-22 15:03:27 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103574  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:46:19 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250421  

added the `begin` and `end` API and changed to std algorithms and removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 181 |+   };
 182 |+ 
 183 |+   if constexpr (!detail::is_static_extents<ExtentsType>::value) {
```

> Username: bassoy  
> Created_at: 2020-03-22 15:05:00 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103712  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:46:23 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250453  

added the `begin` and `end` API and changed to std algorithms and removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 222 |+   });
 223 |+ 
 224 |+   auto num = old_extent.size() - c;
```

> Username: bassoy  
> Created_at: 2020-03-22 15:05:50 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103809  

Could you please comment your code which case is covered by what statements. Better for maintainance, readability and testing.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:46:37 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250561  

done.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 248 |+   }
 249 |+ 
 250 |+   if constexpr( detail::is_static<ExtentsType>::value){
```

> Username: bassoy  
> Created_at: 2020-03-22 15:06:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396103848  

Same as for function `valid`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:46:43 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250614  

added the `begin` and `end` API and changed to std algorithms and removed it.


📁 include/boost/numeric/ublas/tensor/detail/shape_helper.hpp

```diff
  41 |+ {
  42 |+   using type                        = basic_shape;
  43 |+   static constexpr ptrdiff_t rank   = sizeof...(Extents);
```

> Username: bassoy  
> Created_at: 2020-03-22 15:10:36 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396104313  

`rank` should not be a `ptrdiff_t`

> Username: bassoy  
> Created_at: 2020-03-22 15:15:30 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396104803  

We need to solve this with shape_traits instead of encodings such as `dynamic_rank=-1` or `dynamic_extent`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:47:04 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250802  

removed whole implementation.

---

📁 include/boost/numeric/ublas/tensor/detail/shape_helper.hpp

```diff
  65 |+ **/
  66 |+ template<ptrdiff_t... Extents>
  67 |+ using make_static_basic_shape_t = typename basic_shape< Extents... >::type;
```

> Username: bassoy  
> Created_at: 2020-03-22 15:12:39 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396104505  

why is `make_static_basic_shape_t` not `static_basic_shape_t` ?

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:47:14 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402250885  

removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/shape_helper.hpp

```diff
  74 |+ **/
  75 |+ template<ptrdiff_t start, ptrdiff_t end>
  76 |+ using make_dynamic_basic_shape_impl_t = typename make_dynamic_basic_shape_impl< start, end >::type;
```

> Username: bassoy  
> Created_at: 2020-03-22 15:16:26 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396104917  

seems that these are helper functions

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:47:31 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402251052  

removed the whole helper functions.

---

📁 include/boost/numeric/ublas/tensor/detail/shape_helper.hpp

```diff
  13 |+ #define _BOOST_NUMERIC_UBLAS_TENSOR_SHAPE_HELPER_HPP_
  14 |+ 
  15 |+ #include <boost/numeric/ublas/tensor/fwd.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-22 15:19:09 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396105232  

Please remove the `fwd.hpp` file and include only the required forward declarations inside this file as having one forward declaration file is really bad for maintainance and also for the compile time.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:47:51 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402251194  

removed it with `fwd.hpp`.


📁 include/boost/numeric/ublas/tensor/dynamic_extents.hpp

```diff
  37 |+ template<class int_type>
  38 |+ class basic_extents
  39 |+ {
```

> Username: bassoy  
> Created_at: 2020-03-22 15:30:58 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106490  

Can't we set a default value like   
```cpp  
template<class int_type = std::size_t>  
class basic_extents  
```  
  
and then rename `basic_extents` to `dynamic_extents` so that  we can write `auto shape = dynamic_extents<>(3,4,2)`

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:52:45 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402253749  

removed the `shape` in place of it we use `detail::dynamic_extents_impl`. But the problem with rename `basic_extents` with `dynamic_extents` is with `basic_fixed_rank_extents`, we cannot have the same type alias for two different types.

> Username: bassoy  
> Created_at: 2020-04-02 12:06:26 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402261148  

`dynamic_extents<4>` is a different type than `dynamic_extents<>`

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:17:11 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402267045  

```cpp  
using dynamic_extents = basic_extents<>;  
  
template<size_t R>  
using dynamic_extents = basic_fixed_rank_extents<size_t, R>;  
```  
  
gives redefinition of 'dynamic_extents' as different kind of symbol


📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
  15 | #include <cstddef>
  16 | #include <boost/numeric/ublas/expression_types.hpp>
  17 |+ #include "fwd.hpp"
```

> Username: bassoy  
> Created_at: 2020-03-22 15:31:51 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106581  

Please remove `fwd.hpp` and include the necessary forward declarations.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:52:52 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402253827  

removed it.


📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  84 |+ 			: _base(s.size(),1)
  85 |+ 	{
  86 |+ 		if( BOOST_UBLAS_TENSOR_UNLIKLY( s.empty() ) )
```

> Username: bassoy  
> Created_at: 2020-03-22 15:32:55 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106721  

Please remove this, as we do not know what the behavior of the user is.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:53:12 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402254025  

removed whole config with it.

---

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  90 |+ 			throw std::runtime_error("Error in boost::numeric::ublas::basic_strides() : shape is not valid.");		
  91 |+ 
  92 |+ 		if( BOOST_UBLAS_TENSOR_UNLIKLY( is_vector(s) || is_scalar(s) ) )
```

> Username: bassoy  
> Created_at: 2020-03-22 15:33:08 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106758  

Same as above

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:53:17 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402254060  

removed whole config with it.

---

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  87 |+ 			return;
  88 |+ 
  89 |+ 		if( BOOST_UBLAS_TENSOR_UNLIKLY( !valid(s) ) )
```

> Username: bassoy  
> Created_at: 2020-03-22 15:33:16 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106789  

Same as above.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:53:22 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402254114  

removed whole config with it.

---

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  93 |+ 			return;
  94 |+ 
  95 |+ 		if( BOOST_UBLAS_TENSOR_UNLIKLY( this->size() < 2 ) )
```

> Username: bassoy  
> Created_at: 2020-03-22 15:33:29 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396106801  

Same as above.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:53:27 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402254148  

removed whole config with it.


📁 include/boost/numeric/ublas/tensor/fwd.hpp

```diff
   1 |+ //
```

> Username: bassoy  
> Created_at: 2020-03-22 15:38:38 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396107320  

Please put all the necessary forward declarations inside the header files. This file has a bad effect on maintainance and compile time.

> Username: amitsingh19975  
> Created_at: 2020-04-02 11:53:50 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402254349  

done.


📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 323 |+ 				strides_ = strides_type(extents_);
 324 |+ 				data_    = array_type(v.data());
 325 |+ 			}
```

> Username: bassoy  
> Created_at: 2020-03-22 15:49:16 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108463  

I am not sure if this is a good approach. I would have liked to see **template specialization** instead of `if constexpr` inside the tensor template. The code is less readable now.

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:34:54 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402277843  

later stage after decoupling.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 362 |+ 				data_    = std::move(v.data());
 363 |+ 				strides_ = strides_type(extents_);
 364 |+ 			}
```

> Username: bassoy  
> Created_at: 2020-03-22 15:49:27 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108481  

Same as above.

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:35:02 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402277923  

later stage after decoupling.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 399 |+ 				strides_ = strides_type(extents_);
 400 |+ 				data_ = array_type(v.data());
 401 |+ 			}
```

> Username: bassoy  
> Created_at: 2020-03-22 15:49:45 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108515  

Same issue here.

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:35:09 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402277992  

later stage after decoupling.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 435 |+ 				strides_ = strides_type(extents_);
 436 |+ 				data_    = std::move(v.data());
 437 |+ 			}
```

> Username: bassoy  
> Created_at: 2020-03-22 15:49:57 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108534  

See previous comment

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:35:15 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402278051  

later stage after decoupling.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 466 | 				 other.data(), other.strides().data());
 467 |+ 		}
 468 |+ 
```

> Username: bassoy  
> Created_at: 2020-03-22 15:50:11 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108566  

See previous comment.

> Username: bassoy  
> Created_at: 2020-04-02 12:10:06 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402263241  

This will be done at a later stage propably using CRTP.

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:37:22 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402279293  

removed unnecessary constexpr if because of APIs `begin` and `end` except resizing which will done at a later stage.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  80 |- 		public detail::tensor_expression<tensor<T, F, A>,tensor<T, F, A>>
  72 |+ 		public detail::tensor_expression<tensor<T, E, F, A>,tensor<T, E, F, A>>
  73 | {
```

> Username: bassoy  
> Created_at: 2020-03-22 15:53:10 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396108901  

We should partially specialize tensor template for static and dynamic extents.

> Username: bassoy  
> Created_at: 2020-04-02 12:09:53 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402263129  

This will be done at a later stage propably using CRTP.


📁 test/tensor/test_tensor.cpp

```diff
  14 | #include <random>
  15 | #include <boost/numeric/ublas/tensor/tensor.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-22 15:54:29 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r396109042  

Is the tensor also tested with static extents and dynamic extents with static rank?

> Username: amitsingh19975  
> Created_at: 2020-03-24 11:36:18 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r397086173  

I have tested with static extents but not with dynamic extents with static rank. I have to write now.

> Username: bassoy  
> Created_at: 2020-04-02 12:08:09 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402262198  

What is the status here?

> Username: amitsingh19975  
> Created_at: 2020-04-02 12:10:44 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402263576  

I have written the test because of which tests are timing out.


---

## Review 9 [Changes requested]

> Username: bassoy  
> Created_at: 2020-03-30 19:13:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-384030536  

Main discussion points:  
  
- The SFINAE idiom with `enable_if`is used many times. I am not sure if this is needed. I recommend to go with function overloading for our specific types.  
- Some recursively defined parameter pack computations have unnecessary helper classes.  
- BOOST_UBLAS_INLINE or such defines should not be used

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  33 |+ 
  34 |+   template<size_t... N> 
  35 |+   struct number_list{
```

> Username: bassoy  
> Created_at: 2020-03-30 16:32:42 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400330567  

Why do we need `number_list` when we have `std::integer_sequence` or `std::index_sequence`?

> Username: amitsingh19975  
> Created_at: 2020-03-30 19:26:24 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400439410  

I forgot about it. I will replace it.

> Username: bassoy  
> Created_at: 2020-03-31 09:14:43 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400760715  

ok.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:57:04 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400853554  

I'm removing it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
  50 |+ 
  51 |+   template<typename T, size_t... E>
  52 |+   constexpr auto number_list_to_static_extents(number_list<E...> const&) -> basic_static_extents<T,E...>;
```

> Username: bassoy  
> Created_at: 2020-03-30 16:35:51 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400332669  

Why do we need a conversion of types here?

> Username: amitsingh19975  
> Created_at: 2020-03-30 19:34:10 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400443721  

after squeezing it we get number_list but we want static_extents for this purpose I needed this conversing. For needing to convert it number_list, I wanted to be generic so I could plug and play with any similar type.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:57:39 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400853884  

I removed it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 143 |+ 
 144 |+     // copying non 1s to the new extents
 145 |+     std::copy_if(e.begin(), e.end(), std::back_inserter(n_extents), [](auto const& el){
```

> Username: bassoy  
> Created_at: 2020-03-30 16:47:04 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400340308  

Please allocate and reserve space for `n_extents` even it means to iterate multiple times over a small vector.  
1. count all not `1` of `e`   
2. allocate space for `n_extents` with at least two elements initialized to `0`  
3. copy from `e` to `n_extents`   
4. insert `1` if there are any `0` left using the `at` or `[]`

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:58:15 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400854280  

I changed according to your recommendation.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 197 |+     return true;
 198 |+   }
 199 |+   return ( e.size() > typename ExtentsType::size_type(1) ) &&
```

> Username: bassoy  
> Created_at: 2020-03-30 16:57:38 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400347349  

Is it possible to code with three lines here?  
Seems to do the same job and more readable.  
```cpp  
if (e.size() == 0ul) return false;   
if (e.size() == 1ul) return e[0] == 1u;  
if (e.size() >  1ul) return std::all_of(e.begin(), e.end(), [](auto a) { return a > 0u; });  
```  
also I would use `all_of` instead of `none_of` because one might have used extents with negative values. the user gets a warning here, but still...

> Username: amitsingh19975  
> Created_at: 2020-03-30 19:58:29 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400456870  

I think extents are non-neg values. But if you want I will change it all_of.

> Username: bassoy  
> Created_at: 2020-03-31 11:36:49 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400842580  

Correct. However, the template parameter `ExtentsType` are not restricted to only positive values.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:39:19 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400843971  

we are static asserting about the sign. So no one can create neg valued extents.

> Username: amitsingh19975  
> Created_at: 2020-03-31 16:14:21 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401040211  

why don't we use comments for telling the condition because compilers will give a warning about (-Wreturn-type) no return type warning and if someone has enabled warning as the error it will not compile.

> Username: amitsingh19975  
> Created_at: 2020-03-31 16:17:36 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401042400  

Or we could define macro for if-else where else will have condition.

> Username: bassoy  
> Created_at: 2020-04-01 13:52:59 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401632461  

I am okay with `[](auto a) { return a != 0u; }`.   
Do not like macro.  
I think `[](auto a) { return a > 0u; }` will not hurt.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 191 |+ 
 192 |+ /** @brief Returns true if size > 1 and all elements > 0 or size == 1 && e[0] == 1 */
 193 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
```

> Username: bassoy  
> Created_at: 2020-03-30 16:59:47 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400348792  

I am not sure if we need to use SFINAE here as we do not have function overloads.  
I think it would be better to allow any `typename ExtentsType` and then to have a `static_assert` inside the functions.  
Later on we might define concepts for that.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:58:48 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400854656  

removed it and added static_assert.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 211 |+ 
 212 |+ template <class T
 213 |+   , typename std::enable_if<
```

> Username: bassoy  
> Created_at: 2020-03-30 17:00:04 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400349012  

See previous comments about SFINAE.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:59:11 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400854900  

resolved it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 240 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
 241 |+ BOOST_UBLAS_INLINE 
 242 |+ constexpr bool is_scalar(ExtentsType const &e) {
```

> Username: bassoy  
> Created_at: 2020-03-30 17:00:25 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400349259  

See previous comments about SFINAE.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:59:16 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400854955  

resolved it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 241 |+ BOOST_UBLAS_INLINE 
 242 |+ constexpr bool is_scalar(ExtentsType const &e) {
 243 |+   if ( e.size() == typename ExtentsType::size_type(0) ){
```

> Username: bassoy  
> Created_at: 2020-03-30 17:01:15 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400349773  

See previous comments about more readability of small functions.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:59:33 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855132  

I will do it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 262 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
 263 |+ BOOST_UBLAS_INLINE 
 264 |+ constexpr bool is_free_scalar(ExtentsType const &e) {
```

> Username: bassoy  
> Created_at: 2020-03-30 17:01:38 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400350006  

See previous comments about SFINAE.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:59:57 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855357  

remove the function as it was irrelevant.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 257 |+  *
 258 |+  * @code auto expr = 5 * tensor<int>{shape{3,3}}; @endcode
 259 |+  *
```

> Username: bassoy  
> Created_at: 2020-03-30 17:04:20 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400351685  

Why do we need free scalars?  
Expressions could be evaluated with scalars also before.

> Username: amitsingh19975  
> Created_at: 2020-03-30 20:34:53 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400476931  

It came due Ashar's expression template and I forgot to remove it.

> Username: bassoy  
> Created_at: 2020-03-31 11:30:29 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400839330  

ok

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 274 |+ constexpr bool is_vector(ExtentsType const &e) {
 275 |+   
 276 |+   if (e.size() == typename ExtentsType::size_type(0)) {
```

> Username: bassoy  
> Created_at: 2020-03-30 17:04:47 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400351947  

See previous comments about more readability of small functions.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:00:26 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855609  

I will do it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 270 |+  * @returns true if (1,n,[1,...,1]) or (n,1,[1,...,1]) with n > 1
 271 |+  */
 272 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
```

> Username: bassoy  
> Created_at: 2020-03-30 17:04:59 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400352063  

See previous comments about SFINAE.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:00:37 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855708  

resolved it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 294 |+  * @returns true if (m,n,[1,...,1]) with m > 1 and n > 1
 295 |+  */
 296 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
```

> Username: bassoy  
> Created_at: 2020-03-30 17:05:22 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400352263  

The same here.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:00:43 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855749  

resolved it.

---

📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 349 |+ template <class ExtentsType, typename std::enable_if<detail::is_extents<ExtentsType>::value, int>::type = 0>
 350 |+ BOOST_UBLAS_INLINE
 351 |+ constexpr auto product(ExtentsType const &e) {
```

> Username: bassoy  
> Created_at: 2020-03-30 17:05:42 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400352451  

same here.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:00:49 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855808  

resolved it.


📁 include/boost/numeric/ublas/tensor/detail/meta_functions.hpp

```diff
  13 |+ #define BOOST_UBLAS_TENSOR_META_FUNCTIONS_HPP
  14 |+ 
  15 |+ #include <boost/numeric/ublas/tensor/fwd.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-30 17:08:09 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400353907  

Please let us get rid of the `fwd.hpp`  
Include all necessary forward declarations inside this header.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:01:05 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400855936  

I will do it.

---

📁 include/boost/numeric/ublas/tensor/detail/meta_functions.hpp

```diff
  10 |+ //
  11 |+ 
  12 |+ #ifndef BOOST_UBLAS_TENSOR_META_FUNCTIONS_HPP
```

> Username: bassoy  
> Created_at: 2020-03-30 17:12:32 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400356604  

Please rename the `meta_functions.hpp` to `tensor_type_traits` or `shape_type_traits`.  
Only include lines `1` to `163`. The rest of the code should be either in a separate file or be placed inside the `static_strides` or `extents`.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:01:22 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400856092  

ok

---

📁 include/boost/numeric/ublas/tensor/detail/meta_functions.hpp

```diff
 159 |+ template <size_t E, size_t... R> struct product_helper_impl<E, R...> {
 160 |+   static constexpr size_t value = E * product_helper_impl<R...>::value;
 161 |+ };
```

> Username: bassoy  
> Created_at: 2020-03-30 17:28:19 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400366780  

You do need always `helper` template for the recursion:  
```cpp  
template<std::size_t Value, std::size_t ... Values>  
struct product    { static inline constexpr auto value = Value * Product<Values...>::value; };  
  
template<std::size_t Value>  
struct product<Value>  { static inline constexpr auto value = Value; };  
  
template<std::size_t ... values>  
inline constexpr auto product_v = typename product<values...>::value;  
```  
  
Please try to apply the above coding technique or something similar to other occurrences in your code.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:02:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400856521  

I'm changing it and adding it in namespace `boost::numeric::ublas::static_traits`

---

📁 include/boost/numeric/ublas/tensor/detail/meta_functions.hpp

```diff
 208 |+ } // namespace boost::numeric::ublas::storage::detail
 209 |+ 
 210 |+ namespace boost::numeric::ublas::detail{
```

> Username: bassoy  
> Created_at: 2020-03-30 17:30:10 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400368072  

should be placed in a `type_traits_xxxx` in a separate header file.  
why and where do we need such traits?

> Username: amitsingh19975  
> Created_at: 2020-03-31 13:54:44 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400932425  

It was for the second phase of GSoC but forgot to remove it.

---

📁 include/boost/numeric/ublas/tensor/detail/meta_functions.hpp

```diff
 175 |+ 
 176 |+ template <typename V, typename E, typename F, typename A>
 177 |+ using tensor_mode_result_t = typename tensor_mode_result<V, E, F, A>::type;
```

> Username: bassoy  
> Created_at: 2020-03-30 17:31:47 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400369071  

Should be placed in the header file of the tensor contraction.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:03:42 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400857326  

ok.


📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
  67 |+     struct static_stride_product<>{
  68 |+       static constexpr auto const value = 1;
  69 |+     };
```

> Username: bassoy  
> Created_at: 2020-03-30 17:34:54 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400371123  

In some previous file you have defined the product function for static extents.  
you should remove one struct.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:03:58 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400857491  

I will refactor the code.

---

📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
  13 |+ #define _BOOST_NUMERIC_UBLAS_TENSOR_STATIC_STRIDE_HELPER_HPP_
  14 |+ 
  15 |+ #include <boost/numeric/ublas/detail/config.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-30 17:39:14 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400373847  

why do you need the `config.hpp`?

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:04:14 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400857627  

I removed it.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:04:34 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400857814  

I was using it for `BOOST_UBLAS_INLINE`

> Username: bassoy  
> Created_at: 2020-04-01 13:55:21 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401634283  

yes, I understand. I am against including BOOST_UBLAS_INLINE.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:08:45 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401644167  

I have removed it and instead add inline keyword.

---

📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
 125 |+ 
 126 |+     template<>
 127 |+     struct is_vector_static_stride_list<> : std::integral_constant<bool,false>{};
```

> Username: bassoy  
> Created_at: 2020-03-30 17:43:31 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400376586  

1. why is the struct named `is_vector_static_stride_list` and what is the relation to strides? Do you mean extents?  
2. could we rename `is_vector_static_stride_list` to `is_vector_static` and place it to `static_strides.hpp` ?

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:04:59 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400858065  

I'm on it.

> Username: amitsingh19975  
> Created_at: 2020-04-01 13:59:22 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401637397  

fixed it.

---

📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
 145 |+     struct is_valid_static_stride_list{
 146 |+       static constexpr bool const value = is_all_ones<E...>::value || is_vector_static_stride_list<E...>::value;
 147 |+     };
```

> Username: bassoy  
> Created_at: 2020-03-30 17:43:55 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400376850  

Same as the previous comments.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:05:37 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400858407  

I'm the process of resolving it.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:10:01 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401645126  

added new `hpp` with the name of `static_traits`.

---

📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
 164 |+   
 165 |+   template<size_t... E>
 166 |+   inline static constexpr bool is_valid_static_stride_list_v = impl::is_valid_static_stride_list<E...>::value;
```

> Username: bassoy  
> Created_at: 2020-03-30 17:45:36 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400377878  

It would be more convenient to place all the helper values near the structs.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:05:45 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400858480  

ok

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:28:49 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401659153  

placed them in `static_traits.hpp`

---

📁 include/boost/numeric/ublas/tensor/detail/static_strides_helper.hpp

```diff
  96 |+     struct gen_all_one_list {
  97 |+       using type = concat_t<gen_all_one_list_t<T, N/2>, gen_all_one_list_t<T, N - N/2>>;
  98 |+     };
```

> Username: bassoy  
> Created_at: 2020-03-30 17:49:02 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400380169  

I like the `concat` function but I am not sure if we need it for the `stride` generation.

> Username: amitsingh19975  
> Created_at: 2020-03-30 20:40:00 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400479730  

it is to generate static strides containing all ones which are not possible with normal means as it needs to be compile time.

> Username: amitsingh19975  
> Created_at: 2020-03-30 20:41:48 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400480674  

```cpp  
  
auto s = gen_all_one_list_t<size_t, 3>{} // static_stride_list<1,1,1>;  
  
```

> Username: bassoy  
> Created_at: 2020-03-31 11:32:28 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400840340  

We have `std::make_integer_sequence` or `std::make_index_sequence`  
Could you use those?

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:36:07 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400842203  

the problem with those is that they generate seq from 0 to N but we want seq containing 1s only.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:11:03 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401645846  

changed the `gen_all_one_list` to `make_sequence_of_ones`.


📁 include/boost/numeric/ublas/tensor/extents.hpp

```diff
  32 |+         }
  33 |+     }
  34 |+     return true;
```

> Username: bassoy  
> Created_at: 2020-03-30 17:57:49 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400385640  

why not use `std::equal` or similar the comparison?

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:13:27 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401647583  

added the `std::equal`.

---

📁 include/boost/numeric/ublas/tensor/extents.hpp

```diff
  21 |+ template <class RExtents, class LExtents,
  22 |+     typename std::enable_if_t< detail::is_extents<RExtents>::value && detail::is_extents<LExtents>::value, int > = 0
  23 |+ >
```

> Username: bassoy  
> Created_at: 2020-03-30 17:58:37 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400386128  

Instead of `enable_if` I recommend to use a `static_assert`.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:14:36 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401648448  

same problem as [issue](https://github.com/boostorg/ublas/pull/80#discussion_r400832647)

> Username: bassoy  
> Created_at: 2020-04-01 15:31:17 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401706824  

ok.

---

📁 include/boost/numeric/ublas/tensor/extents.hpp

```diff
  19 |+ namespace boost::numeric::ublas {
  20 |+ 
  21 |+ template <class RExtents, class LExtents,
```

> Username: bassoy  
> Created_at: 2020-03-30 17:59:13 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400386538  

I am confused with terms `RExtents` and `LExtents`, left and right ?

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:15:55 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401649442  

It was a typo and I fixed it. It was supposed to be left and right.


📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  76 |+ 			std::enable_if_t< 
  77 |+ 				!( is_static_rank<E1>::value && is_static_rank<E2>::value ),
  78 |+ 				int
```

> Username: bassoy  
> Created_at: 2020-03-30 18:32:27 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400406824  

Overloading and using the explicit type is better here instead of using `std::enable_if I guess.  
Is there a way we could make this more explicit?

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:20:00 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401652371  

I overloaded the function.


📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
 131 |+       }
 132 |+       return true;
 133 |+     }
```

> Username: bassoy  
> Created_at: 2020-03-30 18:36:49 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400409351  

Could you use sth like `std::equal` here?  
And I recommend to set the comparison function free.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:20:18 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401652609  

added `std::equal`.

---

📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
  13 |+ #define _BOOST_NUMERIC_UBLAS_TENSOR_STATIC_EXTENTS_HPP_
  14 |+ 
  15 |+ #include <boost/numeric/ublas/detail/config.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-30 18:37:15 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400409602  

Please do not use the `config.hpp`. It will be soon deprecated.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:24:34 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401655893  

removed it.


📁 include/boost/numeric/ublas/tensor/static_functions.hpp

```diff
   1 |+ //
```

> Username: bassoy  
> Created_at: 2020-03-30 18:42:11 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400412466  

I recommend that to place the below function inside the tensor `functions.hpp`.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:27:58 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401658528  

combined them both.


📁 include/boost/numeric/ublas/tensor/strides.hpp

```diff
  33 |+   }
  34 |+   return true;
  35 |+ }
```

> Username: bassoy  
> Created_at: 2020-03-30 18:45:03 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400414150  

I recommend not to use `enable_if` but to use `static_assert` for compile time checks.

> Username: amitsingh19975  
> Created_at: 2020-03-31 11:17:47 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400832647  

I have to use enable_if here because if I don't it will satisfy every type with `operator==` and will give redefinition error. Here we necessarily need concept otherwise it will not work.

> Username: amitsingh19975  
> Created_at: 2020-04-01 14:12:30 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401646912  

add the `std::equal` but could not remove `std::enable_if_t` due to unconstrained input

> Username: bassoy  
> Created_at: 2020-04-01 15:30:50 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401706525  

ok.

---

📁 include/boost/numeric/ublas/tensor/strides.hpp

```diff
  37 |+ template <class LStrides, class RStrides,
  38 |+   typename std::enable_if_t< detail::is_strides<LStrides>::value && detail::is_strides<RStrides>::value, int > = 0
  39 |+ >
```

> Username: bassoy  
> Created_at: 2020-03-30 18:45:39 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400414510  

See previous comments

> Username: amitsingh19975  
> Created_at: 2020-04-01 13:58:54 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401636999  

similar problems as above.

> Username: bassoy  
> Created_at: 2020-04-01 15:31:05 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401706686  

ok.


📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  32 |- template<class T, class F, class A>
  33 |- class tensor;
  21 |+ #include <boost/config.hpp>
```

> Username: bassoy  
> Created_at: 2020-03-30 18:49:04 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400416499  

I recommend not to use a global `config.hpp`

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:06:09 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400858692  

removed it.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 329 |+ 				strides_ = strides_type(extents_);
 330 |+ 			}
 331 |+ 			std::move(v.data().begin(), v.data().end(),data_.begin());
```

> Username: bassoy  
> Created_at: 2020-03-30 18:52:42 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400418679  

This `std::move` does not really move vector `v` to `this`.

> Username: amitsingh19975  
> Created_at: 2020-03-31 12:06:28 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400858870  

changed to raw loop and then moved it.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  72 | class tensor:
  80 |- 		public detail::tensor_expression<tensor<T, F, A>,tensor<T, F, A>>
  73 |+ 		public detail::tensor_expression<tensor<T, E, F, A>,tensor<T, E, F, A>>
```

> Username: bassoy  
> Created_at: 2020-03-30 18:57:33 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400421695  

I am not sure if we need partial specialization or we should handle `extents` and `allocator` types inside the function bodies using `constexpr`.

> Username: amitsingh19975  
> Created_at: 2020-03-30 19:25:31 UTC  
> Updated_at: 2020-04-14 15:49:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r400438909  

we can create different tensor types such as static_tensor and dynamic_tensor but it will violate the DRY principle

> Username: bassoy  
> Created_at: 2020-04-01 13:59:24 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401637415  

discussion postponed.


---

## Review 10 [Changes requested]

> Username: bassoy  
> Created_at: 2020-04-01 15:54:55 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-385713278  

- [x] Please remove `fwd.hpp` if not needed any more  
- [x] I recommend to include `static_strides_helper.hpp` inside `static_strides.hpp`  
- [x] I recommend to rename `static_traits.hpp` to `static_extents_traits.hpp`  
- [x] Please reply to my comment in `extents_functions.hpp`

📁 include/boost/numeric/ublas/tensor/storage.hpp

```diff
  21 |+ #include <type_traits>
  22 |+ #include <unordered_map>
  23 |+ #include <vector>
```

> Username: bassoy  
> Created_at: 2020-04-01 15:43:59 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401716261  

Could you reduce the number of headers here?

> Username: amitsingh19975  
> Created_at: 2020-04-02 07:49:35 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402116315  

It was due to the compressed map now I removed it.


📁 include/boost/numeric/ublas/tensor/detail/extents_functions.hpp

```diff
 149 |+       return squeeze_impl(e.to_dynamic_extents());
 150 |+     }
 151 |+   }
```

> Username: bassoy  
> Created_at: 2020-04-01 15:50:31 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r401721191  

This should be placed inside the corresponding header of the extents definitions.

> Username: amitsingh19975  
> Created_at: 2020-04-02 08:02:20 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402123774  

these are one-time use for a squeeze that's it or helper functions. So there is no need to separate them.


---

## Review 11 [Changes requested]

> Username: bassoy  
> Created_at: 2020-04-05 13:22:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-387032434  

- [ ] minor corrections required  
- [ ] add examples to tensor with new static extents and dynamic extents with static rank  
- [ ] please remove AppVeyor Image with Visual Studio 2017, TOOLSET=msvc-14.1, CXXSTD=17  
- [ ] please add in Travis the `clang` toolset and later versions for `CXXSTD=2a`

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 246 |+   // default constructor
 247 |+   constexpr basic_static_strides(){
 248 |+     		if( Rank == 0 )
```

> Username: bassoy  
> Created_at: 2020-04-03 07:52:00 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r402799029  

This should be an `if constexpr`

> Username: amitsingh19975  
> Created_at: 2020-04-05 15:39:43 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403718560  

forgot to add. Now it's done.

---

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 190 |+   template<typename Layout, typename T, T... E>
 191 |+   inline static constexpr auto strides_helper_v = strides_helper<Layout, T, E...>::value;
 192 |+ 
```

> Username: bassoy  
> Created_at: 2020-04-05 12:41:57 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403696630  

I think the generation of static strides should not take that many lines of code.

> Username: amitsingh19975  
> Created_at: 2020-04-05 15:42:05 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403718823  

I can decrease the lines of code but it will decrease the expressiveness and readability.

> Username: bassoy  
> Created_at: 2020-04-05 16:10:26 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403722273  

I believe that the static stride generation can be implemented with lesser helper structs.  
I did not mean to squeeze the existing code.  
  
We should refactor that part once it is merged into the development branch.

---

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 207 |+ {
 208 |+ 
 209 |+   static constexpr std::size_t const Rank = sizeof...(Extents);
```

> Username: bassoy  
> Created_at: 2020-04-05 12:43:51 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403696890  

Should not be named `Rank` because strides, shapes and layouts do not have a rank.  
Please use `_size`.

> Username: amitsingh19975  
> Created_at: 2020-04-05 15:43:01 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403718928  

ok.


📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
  31 |+ struct basic_static_extents{
  32 |+ 
  33 |+   static constexpr auto Rank = sizeof...(E);
```

> Username: bassoy  
> Created_at: 2020-04-05 12:44:18 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403696951  

Should not be named `Rank` because strides, shapes and layouts do not have a rank.  
Please use `_size`.

> Username: amitsingh19975  
> Created_at: 2020-04-05 15:43:11 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403718943  

ok.


📁 include/boost/numeric/ublas/tensor/detail/static_extents_traits.hpp

```diff
  43 |+   struct product< basic_static_extents<ExtentsType> >{
  44 |+     static constexpr auto const value = ExtentsType(0) ;
  45 |+   };
```

> Username: bassoy  
> Created_at: 2020-04-05 12:46:47 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403697215  

The `product` struct is not a traits type. This should be placed inside the static_extents file.

> Username: amitsingh19975  
> Created_at: 2020-04-05 15:43:27 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r403718982  

ok.


---

## Comment 12

> Username: amitsingh19975  
> Created_at: 2020-04-06 17:23:19 UTC  
> Updated_at: 2020-04-07 09:11:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-609928220  

> * [x]  minor corrections required  
> * [x]  add examples to tensor with new static extents and dynamic extents with static rank  
> * [x]  please remove AppVeyor Image with Visual Studio 2017, TOOLSET=msvc-14.1, CXXSTD=17  
> * [x]  please add in Travis the `clang` toolset and later versions for `CXXSTD=2a`  
* [x]  waiting for final build

---

## Review 13 [Commented]

> Username: bassoy  
> Created_at: 2020-04-11 11:45:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-391759322  

According to Jamfile, new unit-tests are not tested.

📁 test/tensor/Jamfile

```diff
  54 |+         #   test_static_operators_comparison.cpp
  55 |+         #   test_fixed_rank_expression_evaluation.cpp
  56 |+         #   test_static_expression_evaluation.cpp
```

> Username: bassoy  
> Created_at: 2020-04-11 11:41:22 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407053611  

is there a reason why the tests are commented?

> Username: amitsingh19975  
> Created_at: 2020-04-11 11:48:14 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407054266  

My computer was running out of memory if I uncomment them. Therefore I don't know appveyor or travis will do the same.

> Username: amitsingh19975  
> Created_at: 2020-04-11 11:49:03 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407054389  

It was giving me length exception for vector when resize.

> Username: bassoy  
> Created_at: 2020-04-11 13:06:11 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407061848  

Okay I will take a look at the performance issues.

> Username: amitsingh19975  
> Created_at: 2020-04-11 13:07:22 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407061964  

I'm checking at my end if there is a bug or is it a memory issue.

> Username: bassoy  
> Created_at: 2020-04-11 14:59:43 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407073580  

Compiling with multiple cores, i.e. `-j8` I get:  
```bash  
g++-7: internal compiler error: Killed (program cc1plus)  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <file:///usr/share/doc/gcc-7/README.Bugs> for instructions.  
```  
Using only two cores, cc1plus is killed when compiling   
```bash   
test_fixed_rank_operators_comparison.cpp  
```  
or  
```bash   
test_fixed_rank_operators_arithmetic.cpp  
```  
I think it might help to instantiate less templates.

> Username: amitsingh19975  
> Created_at: 2020-04-11 16:12:26 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407081158  

I think I found the problem. It is giving error because defining fixture before BOOST_AUTO_TEST_CASE.

> Username: amitsingh19975  
> Created_at: 2020-04-11 16:14:33 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407081403  

I just tested it and it's working fine now.


---

## Review 14 [Commented]

> Username: bassoy  
> Created_at: 2020-04-12 09:52:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-391840316  

I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
Please also comment on the possible bug and the affected files so we can narrow the problem down.

---

## Comment 15

> Username: amitsingh19975  
> Created_at: 2020-04-12 09:54:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612590389  

> I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
> Please also comment on the possible bug and the affected files so we can narrow the problem down.  
  
appveyor is running out of heap memory right now.

---

## Comment 16

> Username: bassoy  
> Created_at: 2020-04-12 11:59:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612603609  

> > I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
> > Please also comment on the possible bug and the affected files so we can narrow the problem down.  
>   
> appveyor is running out of heap memory right now.  
  
I still recommend it but you are right: Appveyor and Travis machines run out of memory.  
Is the fixed rank tensor implementation perhaps leaking memory?  
  
I recommend to run `test_fixed_rank_operators_arithmetic.cpp` with valgrind or scan-build while using only *two* extents and *two* element types and analyze the memory requirements.

---

## Comment 17

> Username: amitsingh19975  
> Created_at: 2020-04-12 12:35:17 UTC  
> Updated_at: 2020-04-12 12:36:46 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612607704  

> > > I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
> > > Please also comment on the possible bug and the affected files so we can narrow the problem down.  
> >   
> >   
> > appveyor is running out of heap memory right now.  
>   
> I still recommend it but you are right: Appveyor and Travis machines run out of memory.  
> Is the fixed rank tensor implementation perhaps leaking memory?  
>   
> I recommend to run `test_fixed_rank_operators_arithmetic.cpp` with valgrind or scan-build while using only _two_ extents and _two_ element types and analyze the memory requirements.  
  
I had tested for memory leak using clang address sanitizer, scan-build, and xcode instruments but found no memory leak, right now I'm separating tests into smaller units dynamic, fixed rank and static which is much faster to run and might fix memory problem.

---

## Comment 18

> Username: bassoy  
> Created_at: 2020-04-12 12:48:22 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612609183  

> > > > I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
> > > > Please also comment on the possible bug and the affected files so we can narrow the problem down.  
> > >   
> > >   
> > > appveyor is running out of heap memory right now.  
> >   
> >   
> > I still recommend it but you are right: Appveyor and Travis machines run out of memory.  
> > Is the fixed rank tensor implementation perhaps leaking memory?  
> > I recommend to run `test_fixed_rank_operators_arithmetic.cpp` with valgrind or scan-build while using only _two_ extents and _two_ element types and analyze the memory requirements.  
>   
> I had tested for memory leak using clang address sanitizer, scan-build, and xcode instruments but found no memory leak, right now I'm separating tests into smaller units dynamic, fixed rank and static which is much faster to run and might fix memory problem.  
  
Great. I think that the separation will do its job.

---

## Comment 19

> Username: bassoy  
> Created_at: 2020-04-12 13:11:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612612105  

> > > > > I recommend that you first get rid of the tensor-related warnings in Appveyor and Travis.  
> > > > > Please also comment on the possible bug and the affected files so we can narrow the problem down.  
> > > >   
> > > >   
> > > > appveyor is running out of heap memory right now.  
> > >   
> > >   
> > > I still recommend it but you are right: Appveyor and Travis machines run out of memory.  
> > > Is the fixed rank tensor implementation perhaps leaking memory?  
> > > I recommend to run `test_fixed_rank_operators_arithmetic.cpp` with valgrind or scan-build while using only _two_ extents and _two_ element types and analyze the memory requirements.  
> >   
> >   
> > I had tested for memory leak using clang address sanitizer, scan-build, and xcode instruments but found no memory leak, right now I'm separating tests into smaller units dynamic, fixed rank and static which is much faster to run and might fix memory problem.  
>   
> Great. I think that the separation will do its job.  
  
Still the out of memory issue :-(  
  
I recommend to run the tests with fewer ```test_types```, i.e.   
```cpp  
using test_types = zip<int,float>::with_t<boost::numeric::ublas::first_order, boost::numeric::ublas::last_order>;  
```  
  
If that works, you could add   
  
```cpp  
using double_extended = boost::multiprecision::cpp_bin_float_double_extended;  
```

---

## Comment 20

> Username: bassoy  
> Created_at: 2020-04-12 13:58:03 UTC  
> Updated_at: 2020-04-12 13:58:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612618792  

1. You can also reduce the amount of memory needed by reducing the number of cores/threads used to `-j2` or `-j4`.  
2. For some tensor tests with fixed and static extents types I would also leave out the `std::complex`.   
3. Once all tests are running through, we can include `std::complex` and `boost::multiprecision` at some specific tests with fewer tensor instances.

---

## Comment 21

> Username: amitsingh19975  
> Created_at: 2020-04-12 14:06:28 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612620245  

> * You can also reduce the amount of memory needed by reducing the number of cores/threads used to `-j2` or `-j4`.  
> * For some tensor tests with fixed and static extents types I would also leave out the `std::complex`.  
> * Once all tests are running through, we can include `std::complex` and `boost::multiprecision` at some specific tests with fewer tensor instances.  
  
Now it compiles, I reduced the tests and types.

---

## Comment 22

> Username: amitsingh19975  
> Created_at: 2020-04-12 14:09:22 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612620749  

We can create user-based tests, which does not compile on Travis or appveyor but you have run these tests before committing.

---

## Comment 23

> Username: bassoy  
> Created_at: 2020-04-12 14:29:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-612624179  

> We can create user-based tests, which does not compile on Travis or appveyor but you have run these tests before committing.  
  
I do not recommend it. Travis and Appveyor are gate keepers ensuring that pull requests do not break existing code and behavior.  
  
Please include some of the commented extents (finding the compile error limit)   
Please also consider my previous https://github.com/boostorg/ublas/pull/80#issuecomment-612618792

---

## Review 24 [Commented]

> Username: bassoy  
> Created_at: 2020-04-13 20:41:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-392419144  

1. Did you find a bug in  `include/boost/numeric/ublas/tensor/multiplication.hpp` with the indices?  
2. Please reply on my last two or three comments.

📁 test/tensor/test_functions.cpp

```diff
  24 |+ 
  25 |+ #define BOOST_TEST_MODULE TestTensorFunctions
  26 |+ 
```

> Username: bassoy  
> Created_at: 2020-04-13 20:34:09 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407704416  

Can you explain this change?

> Username: amitsingh19975  
> Created_at: 2020-04-13 20:49:33 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407712486  

the program was crashing and giving stack overflow which could be trigger if tensor with std::array is initialized in multiple places. This was happening because a single thread has particular amount of stack available and `test_function.cpp` was very brutal to stack as it has multiple instances of the static tensor of `std::array` so when I separated them, the went away and during in this process I also fixed the `msvc` related warnings.


📁 test/tensor/Jamfile

```diff
  64 |+     [ run test_functions.cpp
  65 |+           unit_test_framework
  66 |+     ]
```

> Username: bassoy  
> Created_at: 2020-04-13 20:40:45 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407707789  

The files `test_function.cpp` and `test_multiplication.cpp` include `static_extents`. Would it be better to split those files, one that is only heaving static extents?

> Username: amitsingh19975  
> Created_at: 2020-04-13 20:51:25 UTC  
> Updated_at: 2020-04-14 15:49:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r407713412  

This was created during `GSoC` so forgot about it. I was also thinking the same thing when I realized this was causing the crash in `msvc`. I will refactor it.


---

## Comment 25

> Username: amitsingh19975  
> Created_at: 2020-04-13 21:00:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#issuecomment-613095657  

> > We can create user-based tests, which does not compile on Travis or appveyor but you have run these tests before committing.  
>   
> I do not recommend it. Travis and Appveyor are gate keepers ensuring that pull requests do not break existing code and behavior.  
>   
> Please include some of the commented extents (finding the compile error limit)  
> Please also consider my previous [#80 (comment)](https://github.com/boostorg/ublas/pull/80#issuecomment-612618792)  
  
I think we have to vigilant around static tensor because it uses `std::array` which in turn uses stack. Most of the cases I think we should be ok with floating-point operations. I did not touch the threads because others also use the same setting not just `tensor` and they might want faster build time for tests and change the threads. I wanted thread agnostic tests.

---

## Review 26 [Commented]

> Username: coder3101  
> Created_at: 2020-05-30 12:35:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/80#pullrequestreview-421402102  

📁 .travis.yml

```diff
  92 |   - cd libs/numeric/ublas
  78 |-   - $BOOST_ROOT/b2 -j 8 test toolset=$TOOLSET cxxstd=$CXXSTD
  93 |+   - travis_wait 120 sleep infinity & $BOOST_ROOT/b2 -j 8 test/tensor toolset=$TOOLSET cxxstd=$CXXSTD
```

> Username: coder3101  
> Created_at: 2020-05-30 12:35:31 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r432838078  

@bassoy I think you missed this while review.  
  
Did you intensionally disable the testing to `non-tensor` based tests?  
  
@amitsingh19975 For Testing on our GSOC fork and speed, We had disabled unit tests of complete uBLAS and only kept tensor tests.  
  
There is no action required from your side when I propose the new CI, I will re-enable the complete unit tests.

> Username: bassoy  
> Created_at: 2020-05-31 14:16:10 UTC  
> Updated_at: 2020-05-31 14:16:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r432951624  

@coder3101   
Thanks for pointing this out. Yes I have missed it when reviewing.

> Username: bassoy  
> Created_at: 2020-05-31 14:16:32 UTC  
> Url: https://github.com/boostorg/ublas/pull/80#discussion_r432951645  

:+1:


---
