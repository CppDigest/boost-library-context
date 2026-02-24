# #90 Introducing a new and versatile way to create the tensor interface. [Merged]

> Username: amitsingh19975  
> Created at: 2020-07-08 11:32:04 UTC  
> Updated at: 2020-12-10 13:28:55 UTC  
> Merged at: 2020-12-10 13:28:55 UTC  
> Closed at: 2020-12-10 13:28:55 UTC  
> Url: https://github.com/boostorg/ublas/pull/90  

Previously the tensor was constraints to three type of tensors  
- Dynamic tensor  
- Static tensor  
- Fixed rank tensor  
which were predefined and were written in stone or has a rigid interface and the user cannot change the storage type or permute the settings.  
  
With the new tensor interface, we can easily new tensor type even custom storage by the user with proper configuration and easily interact with the current tensor environment without any hassle. This makes the new tensor interface very flexible.  
  
There are few downsides right now  
- Had to remove static tensor tests because we introduced new constraints to the interface but it will be fixed in the next pull request which will be all about proper static tensor integration.  
- For a similar reason had to remove a few tests from the fixed rank tensors and will be fixed with the next pull request.  
  
**How to Use the New Interface**  
```cpp  
/// creates the engine traits with extents and strides with dynamic property with the static storage  
using ctype1 = tensor_engine<extents<>, layout::first_order<>, std::array<float,50>>;  
  
/// creates the engine traits with extents  with dynamic property and strides  with static property with the static storage  
using ctype2 = tensor_engine<extents<>, layout::first_order<static_extents<1,2,3,4>>, std::array<float,50>>;  
  
/// creates the engine traits with extents and strides with static property with the dynamic storage  
using ctype3 = tensor_engine<layout::first_order<static_extents<1,2,3,4>>, std::vector<float>>;  
  
using ttype1 = tensor_core<ctype1>;  
using ttype2 = tensor_core<ctype2>;  
using ttype3 = tensor_core<ctype3>;  
  
auto t1 = ttype1{extents<>{1,2,3},1.f};  
auto t2 = ttype2{extents<>{1,2,3,4},1.f};  
auto t3 = ttype3{1.f};  
  
```  
  
Other than these there are a few minor tweaks to improve the consistency of the code and bug fixes.

---

## Comment 1

> Username: coder3101  
> Created_at: 2020-07-11 17:18:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657098166  

@amitsingh19975 Would you please sync with current development branch and resolve the conflicts.

---

## Comment 2

> Username: amitsingh19975  
> Created_at: 2020-07-11 17:37:39 UTC  
> Updated_at: 2020-07-11 20:05:08 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657101019  

> @amitsingh19975 Would you please sync with current development branch and resolve the conflicts.  
  
Sorry about that I cloned the branch before the new pull request. Now I have synced it. Thanks.

---

## Review 3 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-446819199  

Reports by clang tidy

📁 examples/tensor/fixed_rank_tensor.cpp

```diff
  84 |     // Assigning dynamic_tensor into fixed_rank_tensor
  85 |-     ub::dynamic_tensor t5 = dt1;
  85 |+     ub::dynamic_tensor<float> t5 = dt1;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219680  

local copy `t5` of the variable `dt1` is never modified; consider avoiding the copy [performance-unnecessary-copy-initialization,-warnings-as-errors]  
```cpp  
ub::dynamic_tensor<float> t5 = dt1;  
                              ^  
    const                    &  
```  
Reported as error by clang-tidy.

> Username: coder3101  
> Created_at: 2020-07-12 01:42:40 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453254612  

You can suppress this warning by:  
```cpp  
// NOLINTNEXTLINE(performance-unnecessary-copy-initialization)  
ub::dynamic_tensor<float> tf = dt1;  
```


📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
  32 | template <class ExtentsType, std::size_t N>
  35 |- struct basic_fixed_rank_extents
  33 |+ class basic_fixed_rank_extents
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219681  

class `basic_fixed_rank_extents` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,-warnings-as-errors]  
```cpp  
class basic_fixed_rank_extents  
      ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:32:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222002  

added the move constructor.

---

📁 include/boost/numeric/ublas/tensor/fixed_rank_extents.hpp

```diff
  79 |+     {}
  80 |+     
  81 |+     constexpr basic_fixed_rank_extents(basic_fixed_rank_extents && other)
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219682  

move constructors should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
constexpr basic_fixed_rank_extents(basic_fixed_rank_extents && other)  
              ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:33:23 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222055  

added the `noexcept` to move constructor.


📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  72 |+         template< typename T ,std::size_t N, std::size_t R1, std::size_t R2, typename A>
  73 |+         constexpr auto extents_result_tensor_prod( 
  74 |+             basic_fixed_rank_extents<T,R1> const&,
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219684  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
basic_fixed_rank_extents<T,R1> const&,  
                                                 ^  
                                                  /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:34:45 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222170  

If I add the name and don't use the name `msvc` will give the error so I added the `[[maybe_unused]]`.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  97 |+             E1 const& e1, E2 const& e2,
  98 |+             A1 const& a, A2 const&
  99 |+         ){
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219686  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
){  
        ^  
         /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:35:07 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222214  

same as https://github.com/boostorg/ublas/pull/90#discussion_r453222170


📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
  66 | 
  67 |+   constexpr basic_static_extents(basic_static_extents const&) = default;
  68 |+   constexpr basic_static_extents(basic_static_extents &&) = default;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219687  

move constructors should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
constexpr basic_static_extents(basic_static_extents &&) = default;  
            ^  
                                                           noexcept  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:01:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453224398  

added the `noexcept`

---

📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
  69 |+   
  70 |+   constexpr basic_static_extents& operator=(basic_static_extents const&) = default;
  71 |+   constexpr basic_static_extents& operator=(basic_static_extents &&) = default;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219690  

move assignment operators should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
constexpr basic_static_extents& operator=(basic_static_extents &&) = default;  
                                  ^  
                                                                      noexcept  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:01:34 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453224414  

added the `noexcept`


📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 181 | template <class Layout, class T, T... Extents>
 182 |- struct basic_static_strides<basic_static_extents<T,Extents...>, Layout>
 182 |+ class basic_static_strides<basic_static_extents<T,Extents...>, Layout>
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219692  

class `basic_static_strides` defines a copy constructor and a copy assignment operator but does not define a destructor, a move constructor or a move assignment operator [cppcoreguidelines-special-member-functions,-warnings-as-errors]  
```cpp  
class basic_static_strides<basic_static_extents<T,Extents...>, Layout>  
      ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:35:50 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222278  

added all the constructors and the destructor.


📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
  33 |+ 
  34 |+ template< class T >
  35 |+ class tensor_core:
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219695  

class `tensor_core` defines a copy constructor, a copy assignment operator and a move constructor but does not define a destructor or a move assignment operator [cppcoreguidelines-special-member-functions,-warnings-as-errors]  
```cpp  
class tensor_core:  
      ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:36:10 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222312  

same as above.

---

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 105 |+     }
 106 |+ 
 107 |+     constexpr tensor_core( extents_type e, storage_resizable_container_tag )
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219697  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
constexpr tensor_core( extents_type e, storage_resizable_container_tag )  
                                                                           ^  
                                                                            /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:36:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222348  

same as https://github.com/boostorg/ublas/pull/90#discussion_r453222170

---

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 112 |+     {}
 113 |+ 
 114 |+     constexpr tensor_core( extents_type e, storage_static_container_tag )
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219699  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
constexpr tensor_core( extents_type e, storage_static_container_tag )  
                                                                        ^  
                                                                         /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:36:50 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222361  

same as https://github.com/boostorg/ublas/pull/90#discussion_r453222170

---

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 385 |+      */
 386 |+     inline
 387 |+     tensor_core (tensor_core &&v)
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:06:29 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453219700  

move constructors should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
tensor_core (tensor_core &&v)  
    ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 18:37:08 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453222385  

added the `noexcept`.


---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:19:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657106682  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2020-07-11 18:32:25 UTC  
> Updated_at: 2020-10-05 09:33:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657108434  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=h1) Report  
> Merging [#90](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/92177ce9199e9d30f29570940bc6af541601929f?el=desc) will **decrease** coverage by `0.79%`.  
> The diff coverage is `95.41%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/90/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #90      +/-   ##  
===========================================  
- Coverage    95.33%   94.54%   -0.80%       
===========================================  
  Files           22       19       -3       
  Lines         1223     1192      -31       
===========================================  
- Hits          1166     1127      -39       
- Misses          57       65       +8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/multi\_index.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aV9pbmRleC5ocHA=) | `100.00% <ø> (ø)` | |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `85.83% <ø> (-0.84%)` | :arrow_down: |  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX3N0cmlkZXMuaHBw) | `94.28% <85.71%> (-0.96%)` | :arrow_down: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX3N0cmlkZXMuaHBw) | `94.59% <86.66%> (-0.76%)` | :arrow_down: |  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX2V4dGVudHMuaHBw) | `96.15% <92.85%> (+1.86%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/tensor\_core.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci90ZW5zb3JfY29yZS5ocHA=) | `93.18% <93.18%> (ø)` | |  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `94.94% <98.33%> (+0.26%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/algorithms.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9hbGdvcml0aG1zLmhwcA==) | `99.09% <100.00%> (ø)` | |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX2V4dGVudHMuaHBw) | `100.00% <100.00%> (+2.32%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/expression.hpp](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9leHByZXNzaW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [12 more](https://codecov.io/gh/boostorg/ublas/pull/90/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=footer). Last update [92177ce...580bae1](https://codecov.io/gh/boostorg/ublas/pull/90?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 6 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:49:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-446821624  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
  33 |+ 
  34 |+ template< class T >
  35 |+ class tensor_core:
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 18:49:11 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453223329  

class `tensor_core` defines a default destructor, a copy constructor, a copy assignment operator and a move constructor but does not define a move assignment operator [cppcoreguidelines-special-member-functions,-warnings-as-errors]  
```cpp  
class tensor_core:  
      ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:00:16 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453224259  

added the move assignment operator.


---

## Review 7 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:15:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-446823129  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 398 |+      */
 399 |+     inline
 400 |+     tensor_core& operator=(tensor_core &&v)
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:15:16 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453225609  

move assignment operators should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
tensor_core& operator=(tensor_core &&v)  
                 ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:31:32 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453226906  

added the `noexcept`


---

## Review 8 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:43:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-446824606  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
  44 |+     
  45 |+     ~tensor_expression() = default;
  46 |+     tensor_expression(tensor_expression&&) = default;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:43:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453227877  

move constructors should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
tensor_expression(tensor_expression&&) = default;  
    ^  
                                            noexcept  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:43:51 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453227910  

done.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
  45 |+     ~tensor_expression() = default;
  46 |+     tensor_expression(tensor_expression&&) = default;
  47 |+     tensor_expression& operator=(tensor_expression&&) = default;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:43:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453227878  

move assignment operators should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
tensor_expression& operator=(tensor_expression&&) = default;  
                       ^  
                                                       noexcept  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:44:06 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453227923  

done.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
  73 |+     binary_tensor_expression(binary_tensor_expression&& l) noexcept
  74 |       : el(l.el), er(l.er), op(l.op) {}
  75 |+     binary_tensor_expression& operator=(binary_tensor_expression&& l) = default;
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 19:43:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453227879  

move assignment operators should be marked noexcept [performance-noexcept-move-constructor,-warnings-as-errors]  
```cpp  
binary_tensor_expression& operator=(binary_tensor_expression&& l) = default;  
                              ^  
                                                                       noexcept  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 19:47:19 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453228223  

done.


---

## Review 9 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-07-11 20:11:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-446826180  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 164 | 
 154 |-         if (b.size() == 0)
 165 |+         if (b.empty())
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 20:11:39 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230344  

the `empty` method should be used to check for emptiness instead of `size` [readability-container-size-empty,-warnings-as-errors]  
```cpp  
if (b.size() == 0)  
            ^~~~~~~~~~~~~  
            b.empty()  
include/boost/numeric/ublas/vector.hpp:148:11: note: method 'vector'::empty() defined here  
            bool empty () const {  
                 ^  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 20:14:57 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230611  

done.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 685 |-         constexpr auto extents_result_tensor_times_vector(basic_static_extents<T,E0,E...>, 
 686 |-             basic_static_extents<T, O0, OtherE...>, basic_static_extents<T, R...> = basic_static_extents<T>{})
 815 |+         constexpr auto extents_result_tensor_times_vector(
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 20:11:40 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230345  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
constexpr auto extents_result_tensor_times_vector(basic_static_extents<T,E0,E...>,   
                                                                                         ^  
                                                                                          /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 20:15:08 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230635  

done.


📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 241 | 
 238 |-   constexpr basic_static_strides(extents_type const&) noexcept{};
 242 |+   constexpr basic_static_strides([[maybe_unused]] extents_type const& e) noexcept{};
```

> Username: github-actions[bot]  
> Created_at: 2020-07-11 20:11:40 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230346  

all parameters should be named in a function [readability-named-parameter,-warnings-as-errors]  
```cpp  
constexpr basic_static_strides(extents_type const&) noexcept{};  
                                                    ^  
                                                     /*unused*/  
```  
Reported as error by clang-tidy.

> Username: amitsingh19975  
> Created_at: 2020-07-11 20:15:33 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r453230691  

done.


---

## Comment 10

> Username: coder3101  
> Created_at: 2020-07-12 02:20:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657163825  

Here are some of the things you can do in order to completely pass clang-tidy reviews and checks:  
  
1. **An exception maybe thrown by main, which should not throw any exception**. To fix it disable `bugprone-exception-escape` for tensor examples. In order to do so, please modify `.clang-tidy` **file inside examples**. Change it to this content:  
```  
---  
Checks:                 '-*,modernize-*,cppcoreguidelines-*,openmp-*,bugprone-*,performance-*,portability-*,readability-*,-modernize-use-trailing-return-type,-cppcoreguidelines-pro-bounds-pointer-arithmetic,-readability-uppercase-literal-suffix,-readability-braces-around-statements,-cppcoreguidelines-avoid-magic-numbers,-readability-magic-numbers,-bugprone-exception-escape'  
WarningsAsErrors:       '-*,modernize-*,cppcoreguidelines-*,openmp-*,bugprone-*,performance-*,portability-*,readability-*,-modernize-use-trailing-return-type,-cppcoreguidelines-pro-bounds-pointer-arithmetic,-readability-uppercase-literal-suffix,-readability-braces-around-statements,-cppcoreguidelines-avoid-magic-numbers,-readability-magic-numbers,-bugprone-exception-escape'  
HeaderFilterRegex:      'boost\/numeric\/ublas\/tensor\/.*'  
AnalyzeTemporaryDtors:  false  
FormatStyle:            file  
User:                   ublas-developers  
CheckOptions:  
  - key:                modernize-use-nullptr.NullMacros  
    value:              'NULL'  
...  
```  
Reason: `bug-prone-exception-escape` can be solved if all main is wrapped into a `try-catch`. It basically means, `main` should exit only by returning not by throwing any error since many functions can throw an exception in our library. Ideally, they should be caught for an exception. You can see which option is more viable for you!  
  
2. **Do not use else after return**, So, don;t use else after return in the following places:  
  
```  
/home/runner/work/ublas/ublas/include/boost/numeric/ublas/tensor/detail/extents_functions.hpp:232:3: error: do not use 'else' after 'return' [readability-else-after-return,-warnings-as-errors]  
  else if (e.size() == 1u) return e[0] > 1u;  
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/runner/work/ublas/ublas/include/boost/numeric/ublas/tensor/detail/extents_functions.hpp:300:3: error: do not use 'else' after 'return' [readability-else-after-return,-warnings-as-errors]  
  else return std::accumulate(e.begin(), e.end(), 1u, std::multiplies<>()) ;  
```  
  
Just remove `else` from each line. Since control is returned if `if` is true, there is no need for else in the second condition.  
  
3. **Assert decaying array to pointer**. This is not something we are doing and technically clang-tidy shouldn't have reported error/warnings for `assert` function definition. In the expansion of `assert()` some placeholder is being decayed into a pointer. `__extensions`, we have no control over how standard library did it. So we are better off by disable linting for all the assert in the code. by following:  
```cpp  
// NOLINTNEXTLINE(cppcoreguidelines-pro-bounds-array-to-pointer-decay)  
assert(foo == bar);  
```  
Or we can get rid of assert or replace it with something more modern.  
  
4. **function-like macro 'FIRST_ORDER_OPERATOR_RIGHT' used**; I had a talk with Cem regarding this report and he said we should expand and manually replace all macros with their corresponding code.   
  
  
I suggested how to solve them, once done, clang-tidy will pass all tests. I cannot push to `features/tensor_redesign` but, I can propose PR for that branch with above fixes applied from my gsoc20 branch.

---

## Comment 11

> Username: coder3101  
> Created_at: 2020-07-12 07:43:13 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657187290  

I have opened up a pull request #92 for the branch `tensor_redesign`. You should approve and **merge that PR before this one**.

---

## Comment 12

> Username: amitsingh19975  
> Created_at: 2020-07-12 09:14:44 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657195439  

> ```  
> /home/runner/work/ublas/ublas/include/boost/numeric/ublas/tensor/detail/extents_functions.hpp:232:3: error: do not use 'else' after 'return' [readability-else-after-return,-warnings-as-errors]  
>   else if (e.size() == 1u) return e[0] > 1u;  
>   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /home/runner/work/ublas/ublas/include/boost/numeric/ublas/tensor/detail/extents_functions.hpp:300:3: error: do not use 'else' after 'return' [readability-else-after-return,-warnings-as-errors]  
>   else return std::accumulate(e.begin(), e.end(), 1u, std::multiplies<>()) ;  
> ```  
>   
> Just remove `else` from each line. Since control is returned if `if` is true, there is no need for else in the second condition.  
>   
  
I was doing this earlier but after talking to `Cem` we decided to do `else`  then return for readability. That's why I didn't modify this piece of code. If he agrees then do it.

---

## Comment 13

> Username: coder3101  
> Created_at: 2020-07-12 09:50:50 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657199398  

> I was doing this earlier but after talking to `Cem` we decided to do `else` then return for readability. That's why I didn't modify this piece of code. If he agrees then do it.  
  
The `.clang-tidy` file for checks was created by Cem, but he must have missed this check. Let's see, if he says we can disable this check. I wonder why clang-tidy thinks "Using else after a return statement" affects the readability of code!

---

## Comment 14

> Username: amitsingh19975  
> Created_at: 2020-07-12 09:53:10 UTC  
> Updated_at: 2020-07-12 09:54:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-657199720  

> > I was doing this earlier but after talking to `Cem` we decided to do `else` then return for readability. That's why I didn't modify this piece of code. If he agrees then do it.  
>   
> The `.clang-tidy` file for checks was created by Cem, but he must have missed this check. Let's see, if he says we can disable this check. I wonder why clang-tidy thinks "Using else after a return statement" affects the readability of code!  
  
It's a `llvm` thing how it optimizes the code. That's why clang suggests removing `else` block other compilers may have other behavior.

---

## Review 15 [Changes requested]

> Username: bassoy  
> Created_at: 2020-07-19 20:43:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-451146654  

I like this pull request very much. I think that we are heading in the right direction.  
However, there are two major points (find the rest in comments) which requires some deeper discussion.  
  
  
1. I do not recommend to rebind tensor types in the `prod` function. We should rather overload the `prod` function for multiple tensor types, e.g. resizable container, etc.  
2. Also the layout, extents and stride types are orthogonal concepts which is not reflected in the code.   
Could we postpone this topic and make the implementation simpler with the previously existing types?

📁 examples/tensor/fixed_rank_prod_expressions.cpp

```diff
 167 |-         std::cout << "%  C3(k,l,m) = T(k,l,m) + A(i,j,k)*trans(B(j,l,i,m),{2,3,1,4})+ 5;" << std::endl << std::endl;
 168 |-         std::cout << "C3=" << C3 << ";" << std::endl << std::endl;
 169 |- 
```

> Username: bassoy  
> Created_at: 2020-07-19 16:46:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456929826  

Why is this guy deleted?

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:44:55 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457230403  

this was because we don't know if extents will change in size or not and `fixed_rank_extents` is fixed in size which will be fixed in the next pull request when I add support for static prod.

> Username: bassoy  
> Created_at: 2020-08-17 09:26:36 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r471354091  

This should not be deleted. Please provide a solution for `fixed_rank_extents` type tensors .  
It should not be a superior solution but the pull request should not decrease the number of features of existed features.

> Username: amitsingh19975  
> Created_at: 2020-08-17 10:22:49 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r471384007  

added the example with a few changes.


📁 examples/tensor/static_tensor.cpp

```diff
  34 |     **/
  35 |-     auto t1 = ub::static_tensor<float,ub::static_extents<1,2,3,4,5> >{5.f};
  35 |+     auto t1 = ub::static_tensor<float,ub::static_extents<1,2,3,4,5> >{ub::static_extents<1,2,3,4,5>{}, 5.f};
```

> Username: bassoy  
> Created_at: 2020-07-19 16:48:11 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456930001  

Providing `ub::static_extents<1,2,3,4,5>` is redundant.

> Username: bassoy  
> Created_at: 2020-07-19 16:48:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456930020  

Is this required?

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:34:18 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457222695  

This is due to type aliasing. If I add a construction in `tensor_core` it conflicts with other constructors. I was thinking about how to solve it and try to avoid `std::enable_if`.

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:34:50 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457223123  

Most probably I will solve it by next pull request.

> Username: bassoy  
> Created_at: 2020-07-20 12:12:00 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457326130  

I would like to solve it with this pull request as @coder3101 and me would like to use as the lastest development.

> Username: coder3101  
> Created_at: 2020-07-21 00:16:58 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457762321  

Yes, it will be really hard for me to work on if base continues to change significantly.

> Username: amitsingh19975  
> Created_at: 2020-07-21 08:21:48 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457922299  

Don't worry after this it will not change that much.


📁 include/boost/numeric/ublas/tensor/detail/basic_type_traits.hpp

```diff
  72 |+ template <typename T> 
  73 |+ inline static constexpr bool const always_false_v = always_false<T>::value;
  74 |+ 
```

> Username: bassoy  
> Created_at: 2020-07-19 16:49:38 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456930127  

why do we need `always_false` if we have `std::false_type`

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:38:24 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457225701  

The problem with `std::false_type` is it does not delay the `static_assert`. If I use `std::false_type` `static_assert` will trigger even if the code does not run. On the other hand, if I use a template it will delay the evaluation.

> Username: bassoy  
> Created_at: 2020-07-20 12:13:36 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457327178  

I suppose that this should be solved differently - could you tell me where you use it?

> Username: amitsingh19975  
> Created_at: 2020-07-20 12:16:00 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457328765  

It was supposed to be used when I push the next pull request.

> Username: amitsingh19975  
> Created_at: 2020-07-20 12:16:32 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457329134  

It remained there when I was removing the static functions.

> Username: coder3101  
> Created_at: 2020-07-21 00:24:58 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457764612  

> The problem with `std::false_type` is it does not delay the `static_assert`.  
  
Why do you want to delay the `static assert`? An idea on how do you plan to use it will be enough.  
  
> If I use `std::false_type` `static_assert` will trigger even if the code does not run.  
  
Yes, Static assert is compile-time, why not use a runtime `assert` then? Again things will be more clear if you could share the use case.

> Username: amitsingh19975  
> Created_at: 2020-07-21 10:44:49 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r458004532  

Its use was removed right now as I wrote earlier it is a remain of struct or functions which I forgot to remove. Once @bassoy reviews the new change I will remove it. For cases, I can give you some.  
- let's say you have templated functions or structs as of C++17 we don't have concepts which will make the error propagate through the whole instantiation which produces the whole unnecessary error and hard to find the error. So this helps to stop the error because when it hit `static_assert` it will always false stop there and also helps with the better error system.  
```cpp  
/// Make the user clear about the use of this struct and improves the error message.  
template<typename T>  
struct Integer{  
static_assert(always_false_v<T>,"type does not satisfy the condition");  
}  
  
template<>  
struct Integer<int>{};  
  
```  
- If we have a very large `constexpr if` it will help to satisfy the DRY principle. It also acts as a barrier which makes sure you never reach there.  
```cpp  
if consexpr(....){}  
else if consexpr(....){}  
....  
else{  
static_assert(always_false_v<T>,"");  
}  
```

> Username: bassoy  
> Created_at: 2020-08-03 19:56:39 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464630108  

The trait `always_false` is too general and the naming does not provide enough information what is wrong about the type. The information should not be provided with a string.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:37:52 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464958672  

I have removed it from lib.


📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 137 |+     inline decltype(auto) prod( tensor_core< TensorEngine > const &a, 
 138 |+         vector<typename tensor_core< TensorEngine >::value_type, A> const &b, 
 139 |         const std::size_t m)
```

> Username: bassoy  
> Created_at: 2020-07-19 20:16:53 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456950808  

I would rather specialize `prod` for a specific `tensor_engine` types, e.g. for dynamic extents and dynamically resizable container in this case instead of trying to generate an alternative `result_tensor_t` and `t_engine`. I do not think this is user friendly due to implicit conversion, although the type promotion is nicely defined.  
  
Let's do this using function specialization and later on using concepts.

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:39:42 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457226595  

If I overload functions it will force the use of `std::enable_if` which I was trying to avoid.

> Username: bassoy  
> Created_at: 2020-07-20 12:10:56 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457325371  

>   
>   
> If I overload functions it will force the use of `std::enable_if` which I was trying to avoid.  
  
Got it. I remember that I was suggesting to try without SFINAE.   
However, this solution includes too many implications.   
What would be the down-turn if we use `std::enable_if` if at later stages we switch to concepts?

> Username: amitsingh19975  
> Created_at: 2020-07-21 10:50:42 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r458007307  

The main implication is readability and maintainability.  
I can make a separate header and make the specialization of `std::enable_if` which makes it more readable and easier to replace with concepts if you are ok with that.  
  
```cpp  
template<typename T>  
using Extents = std::enable_if_t<is_extents_v<T>>;  
  
template<typename E, typename = Extents<E>>  
void fun(...){...}  
```  
this is more readable easier to replace with concepts.  
  
```cpp  
template<typename T>  
concept Extents = is_extents_v <T>;  
  
template< Extents E>  
void fun(...){...}  
```

> Username: bassoy  
> Created_at: 2020-08-03 19:48:11 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464626346  

I would like to postpone `concept` then because I think this requires more discussions and therefore I would like to go with `std::enable_if`.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:37:19 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464958414  

ok.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 186 |+             >,
 187 |+             rebind_storage_t<c_extents_type,array_type,value_type>
 188 |+         >;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:17:13 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456950838  

See previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:05 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237094  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 229 |+             "error in boost::numeric::ublas::prod(tensor_core const&, matrix const& ): "
 230 |+             "tensor container should be resizable"
 231 |+         );
```

> Username: bassoy  
> Created_at: 2020-07-19 20:19:00 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456950988  

I like this static assert and the requirement to engine to be `resizable`!

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 269 |+                 layout::last_order<c_extents_type>
 270 |+             >,
 271 |+             rebind_storage_t<c_extents_type,array_type,value_type>
```

> Username: bassoy  
> Created_at: 2020-07-19 20:19:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951061  

why do we need to rebind here?

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:53:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457236846  

Because storage type can be user-defined and I wanted to have a standard way to create result type in every function without thinking about anything.

> Username: bassoy  
> Created_at: 2020-07-20 12:15:54 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457328690  

you have included some sort of type promotion. I would like to postpone the topic of type promotion.

> Username: amitsingh19975  
> Created_at: 2020-07-20 13:41:12 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457394366  

There is no type promotion. I'm using `rebind_storage_t` to have a standard way to select the correct container and in the case of static it just gets the new static container with modified size and correct `value_type`.

> Username: bassoy  
> Created_at: 2020-08-03 18:56:44 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464602551  

- Selecting the appropriate container type sounds like type promotion.   
- Deriving the size of a static container with a runtime variable contraction mode seems hard for me.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:47:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464963144  

This will be more useful in static prod because it might use static storage which after contraction needs to change in size so how we do it, there comes the use of `rebind_storage` as we don't want any increase in complexity inside the prod function. To be consistent with the static prod I added it to the dynamic prod functions which don't do any kind of promotion just act as a placeholder.  
  
Use:  
- static prod with static storage => rebinds the storage with the new size  
- static prod with dynamic storage => does not do anything but returns the same storage. This reduces the complexity inside the prod functions.

> Username: bassoy  
> Created_at: 2020-08-18 19:37:09 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r472434430  

Let me phrase it differently. With `rebind_storage_t` the resulting storage type depends on the input storage type. In this case, the user of the library needs to look into `type_traits_tensor.hpp` for understanding the output type.  Moreover, if the user wants to only have statically allocated containers, this rule forces him/her to not use the `prod` function or to change the implementation of `rebind_storage_t`.  
  
Therefore, we should not to have that feature (at least for now) and at most use [tag dispatching](https://www.boost.org/community/generic_programming.html). Again the `prod` function with that signature should only be implemented for containers with dynamic extents and strides.

> Username: amitsingh19975  
> Created_at: 2020-08-19 09:03:28 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r472877203  

Changed it to `rebind_storage_size` for static products with is necessary for it otherwise there is no way to use it with static storage and removed `rebind_storage` which may be confusing instead I used storage traits to rebind in functions for transforming `conj` and `img`.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 408 |+             >,
 409 |+             rebind_storage_t<c_extents_type,array_type,value_type>
 410 |+         >;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:20:06 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951109  

see previous comments

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237289  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 542 |+         >;
 543 |+ 
 544 |+         auto c = tensor_core<t_engine>( nc, value_type{} );
```

> Username: bassoy  
> Created_at: 2020-07-19 20:20:45 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951184  

see previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:33 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237432  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 586 |+             >,
 587 |+             rebind_storage_t<extents_type,array_type,value_type>
 588 |+         >;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:20:59 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951200  

see previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:46 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237566  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 679 |+         >;
 680 |+ 
 681 |+         using tensor_type = tensor_core<t_engine>;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:21:10 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951228  

see previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:50 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237628  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 730 |+         >;
 731 |+ 
 732 |+         using tensor_type = tensor_core<t_engine>;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:21:21 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951236  

see previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:54 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237699  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 782 |+         >;
 783 |+ 
 784 |+         using tensor_type = tensor_core<t_engine>;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:21:30 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951256  

see previous comment

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:54:58 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457237762  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457236846


📁 include/boost/numeric/ublas/tensor/detail/type_traits_tensor.hpp

```diff
  85 |+     {
  86 |+         using type = typename storage_traits<A>::template rebind<ValueType>;
  87 |     };
```

> Username: bassoy  
> Created_at: 2020-07-19 20:23:08 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951405  

Why do we need this.

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:56:59 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457239214  

This is a standard way to get the storage type because the storage may not be `std::array` or `std::vector`.

> Username: bassoy  
> Created_at: 2020-07-20 12:17:03 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457329491  

Why not defining the typical storage_type_traits for container types ?

> Username: amitsingh19975  
> Created_at: 2020-07-20 12:21:27 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457332428  

This will introduce too many variables and `storage_traits` already exists. I can move this to new header `storage_type_traits`.

> Username: coder3101  
> Created_at: 2020-07-21 01:18:17 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457779184  

I like this trait, except for its location. Amit did a great job at making sure that all containers run fine. The most useful use case I can come up with is that we can use `CUDA Thrust` Vectors easily ( `host_vector` & `device_vector`). See [this](https://docs.nvidia.com/cuda/thrust/index.html) . If we restrict to standard types, we will again need it someday when adding tensor to work on GPUs.

> Username: bassoy  
> Created_at: 2020-08-03 19:59:08 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464631192  

I was talking about `rebing_storage`.


📁 include/boost/numeric/ublas/tensor/tags.hpp

```diff
  26 |+     struct storage_non_seq_container_tag{};
  27 |+ 
  28 |+ } // namespace boost::numeric::ublas::tag
```

> Username: bassoy  
> Created_at: 2020-07-19 20:25:17 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951616  

:+1:


📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  73 |+             };
  74 |+ 
  75 |+         };
```

> Username: bassoy  
> Created_at: 2020-07-19 20:26:06 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456951706  

I would like to have this in a separate `layout.hpp` file.

> Username: amitsingh19975  
> Created_at: 2020-07-20 09:57:22 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457239520  

ok.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  46 |+             };
  47 |+ 
  48 |+         };
```

> Username: bassoy  
> Created_at: 2020-07-19 20:32:49 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456952367  

`layout` and `extents` are orthogonal concepts where the definition of layout should not depend on the type of extents.   
the implementation does not reflect that. Also, I recommend not to have `strides` struct inside a `layout`.

> Username: amitsingh19975  
> Created_at: 2020-07-20 10:00:00 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457241360  

If the decouple them then I have to add a new template parameter and construction of strides depends on the layout so they are interdependent on each other. I can separate them if you want.

> Username: bassoy  
> Created_at: 2020-07-20 12:17:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457329892  

yes - please. but why was this necessary at all?

> Username: amitsingh19975  
> Created_at: 2020-07-20 12:26:52 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457336119  

to provide the construction of tensor with different strides and remove the redundancy to provide the layout once and again when providing the strides because of the interface we discussed.

> Username: amitsingh19975  
> Created_at: 2020-07-20 15:28:20 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457494409  

removed it and added a new header file 'layout.hpp' and template argument. Made a few changes to the `type_traits_strides` to adjust with the template argument `StrideType`.

---

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
 114 |+     template<typename LayoutType, typename StorageType>
 115 |+     struct tensor_engine<LayoutType, StorageType>{
 116 |+         using extents_type 	        = typename layout::extract_strides_t<void,LayoutType>::extents_type;
```

> Username: bassoy  
> Created_at: 2020-07-19 20:33:44 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456952437  

the type of a shape should not depend on an layout type

> Username: amitsingh19975  
> Created_at: 2020-07-20 10:01:03 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457242154  

same as https://github.com/boostorg/ublas/pull/90#discussion_r457241360


📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 644 |+         if(p != this->size())
 645 |+             this->data_.resize (p, v);
 646 |+     }
```

> Username: bassoy  
> Created_at: 2020-07-19 20:35:02 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r456952580  

should be a free standing function.

> Username: amitsingh19975  
> Created_at: 2020-07-20 10:02:36 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457243244  

This will be a free function in the next pull request because I didn't want to mix this pull request with freeing the function.

> Username: bassoy  
> Created_at: 2020-07-20 12:17:57 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457330040  

👍


---

## Comment 16

> Username: bassoy  
> Created_at: 2020-07-19 20:44:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-660706165  

@coder3101 please also start to review. thanks.

---

## Comment 17

> Username: coder3101  
> Created_at: 2020-07-20 11:42:08 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-660976682  

By design it follows the paper you shared for  using engine types and that's something good. I will review it thoroughly tonight.

---

## Review 18 [Commented]

> Username: coder3101  
> Created_at: 2020-07-21 01:33:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-452066075  

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  46 |                                 "Static error in boost::numeric::ublas::basic_strides: type must be of type unsigned integer.");
  49 |-     static_assert(std::is_same<__layout,first_order>::value || std::is_same<__layout,last_order>::value,
  47 |+     static_assert(std::is_same<__layout,layout::first_order>::value || std::is_same<__layout,layout::last_order>::value,
```

> Username: coder3101  
> Created_at: 2020-07-21 01:33:43 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457783661  

I would not use `__layout`. Starting with two underscores is reserved for Implementation. Any problem with `_layout` (Single underscore?)

> Username: amitsingh19975  
> Created_at: 2020-07-21 08:19:11 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457920677  

I know but I did not change it because it present already. Even field members should start or end with any kind of underscore for same reason and to follow the convention I used it in the new structs. If @bassoy wants I can change that in all the places. Thanks for pointing it out I was meaning to ask @bassoy about it.

> Username: bassoy  
> Created_at: 2020-08-03 18:45:39 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464597106  

I am okay with double underscore as it is also used in the gcc standard library implementation.   
However, you could do static asserts after type declaration in line 58.

> Username: coder3101  
> Created_at: 2020-08-04 04:55:23 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464798806  

Resolved

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:28:03 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464953993  

My problem with it is if we try to follow a compiler way of declaring functions, structs, or variables it will in the future collide. So we have to stay away from this kind of practice as they are reserved for the compilers, not for others.

> Username: bassoy  
> Created_at: 2020-08-18 19:08:32 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r472419040  

Yes, you are right because they could be used for the C++ implementation.


---

## Review 19 [Commented]

> Username: coder3101  
> Created_at: 2020-07-21 01:35:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-452066679  

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  90 |-             throw std::runtime_error("Error in boost::numeric::ublas::basic_strides() : size of strides must be greater or equal 2.");
  90 |+             throw std::runtime_error("Error in boost::numeric::ublas::basic_strides(ExtentsType const&) : "
  91 |+                 "size of strides must be greater or equal 2."
```

> Username: coder3101  
> Created_at: 2020-07-21 01:35:45 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457784221  

```diff  
-                "size of strides must be greater or equal 2."  
+                "size of strides must be greater or equal to 2."  
```

> Username: amitsingh19975  
> Created_at: 2020-07-21 08:19:30 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457920863  

It must be typo.

> Username: amitsingh19975  
> Created_at: 2020-07-21 08:19:55 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r457921132  

Thanks I will update it soon.

> Username: amitsingh19975  
> Created_at: 2020-07-21 14:14:31 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r458130024  

fixed it.


---

## Comment 20

> Username: coder3101  
> Created_at: 2020-08-02 07:07:22 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-667638175  

@bassoy can you please review again or continue the conversation in review comments.

---

## Review 21 [Changes requested]

> Username: bassoy  
> Created_at: 2020-08-03 19:44:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460259876  

I like the changes.  
  
However, I have still doubts about rebinding tensor types inside `prod` functions with variable constraction modes `m`, `n`, ... which is why static sized tensors cannot be used for `prod`.  
  
Therefore, I suggest to reduce their applicability either by using `std::enable_if` or `static_assert` the required `tensor_engine`

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
  98 |+         }else {
  99 |+             std::transform(s.rbegin(), s.rend() - 1, _base.rbegin(), _base.rbegin() + 1, std::multiplies<value_type>{});
 100 |         }
```

> Username: bassoy  
> Created_at: 2020-08-03 18:46:11 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464597377  

nice!


---

## Review 22 [Commented]

> Username: coder3101  
> Created_at: 2020-08-04 04:58:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460495850  

📁 include/boost/numeric/ublas/tensor/dynamic_strides.hpp

```diff
 209 |+     }
 210 |     
 190 |- protected:
```

> Username: coder3101  
> Created_at: 2020-08-04 04:58:03 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464799476  

I don't understand why were these `protected` members in the beginning. C++ ISO defers using protected members.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:30:34 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464955182  

The earlier version of tensor implementation had to use the base class data members to avoid friend class I had to use it.


---

## Review 23 [Commented]

> Username: coder3101  
> Created_at: 2020-08-04 05:20:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460502710  

📁 include/boost/numeric/ublas/tensor/static_extents.hpp

```diff
 139 |+ template<typename ExtentsType, ExtentsType... Es>
 140 |+ struct static_product< basic_static_extents<ExtentsType, Es...> >
 141 |+   : std::integral_constant< ExtentsType, (... * Es) >
```

> Username: coder3101  
> Created_at: 2020-08-04 05:20:12 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464805573  

Nice, use of fold expressions.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:32:14 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464955987  

I did not use it earlier because of `msvc` which was giving the error. Now we have changed the 'msvc' version and they have implemented the fold expression I can use it.


---

## Review 24 [Commented]

> Username: coder3101  
> Created_at: 2020-08-04 05:33:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460506920  

📁 include/boost/numeric/ublas/tensor/strides.hpp

```diff
  87 |+     
  88 |+     using value_type = typename Stride::value_type;
  89 |+     std::array<value_type, sizeof...(is)> i = {is...};
```

> Username: coder3101  
> Created_at: 2020-08-04 05:33:20 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464809275  

Performance enhancement. :+1:


---

## Review 25 [Commented]

> Username: coder3101  
> Created_at: 2020-08-04 05:45:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460511262  

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
  26 |+ #include <boost/numeric/ublas/tensor/dynamic_extents.hpp>
  27 |+ #include <boost/numeric/ublas/tensor/strides.hpp>
  28 |+ #include <boost/numeric/ublas/tensor/index.hpp>
```

> Username: coder3101  
> Created_at: 2020-08-04 05:45:53 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464813044  

Should we internally use `"something"` instead of `<something>` for headers?   
  
The benefit would be that we can easily determine if a file is ublas header or one of ublas dependencies or standard.  
  
Also, since "" includes are searched from current working directory first. Most of the IDE can find headers easily and we can type less long headers.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:33:53 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464956802  

This allows us to use any component of the tensor lib anywhere or makes it reusable.


---

## Review 26 [Commented]

> Username: coder3101  
> Created_at: 2020-08-04 05:54:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-460514600  

📁 include/boost/numeric/ublas/tensor/tensor_core.hpp

```diff
 227 |+             throw std::runtime_error("boost::numeric::ublas::tensor_core(extents_type,array_type): "
 228 |+                 "array size mismatch with extents"
 229 |+             );
```

> Username: coder3101  
> Created_at: 2020-08-04 05:54:51 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464815814  

```diff  
-            );  
+            throw std::runtime_error("boost::numeric::ublas::tensor_core(extents_type,array_type): "  
+                "array size mismatch with extents");  
```  
  
Readability.

> Username: amitsingh19975  
> Created_at: 2020-08-04 10:35:24 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r464957552  

'clang format' will restructure it. So it's useless to format it.


---

## Review 27 [Changes requested]

> Username: coder3101  
> Created_at: 2020-08-05 02:14:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-461292447  

📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
  45 |+     struct tensor_engine<ExtentsType, LayoutType, StorageType>
  46 |+         : tensor_engine< ExtentsType, LayoutType, strides<ExtentsType>, StorageType >
  47 |+     {};
```

> Username: coder3101  
> Created_at: 2020-08-05 02:11:26 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r465430701  

Put `tensor_engine` in a separate file. Preferably `tensor/tensor_engine.hpp`.  
  
This is so that it can be inherited to create other engines. Like `matrix_engine` or `vector_engine`.

> Username: amitsingh19975  
> Created_at: 2020-08-05 12:08:44 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r465679289  

ok.


---

## Comment 28

> Username: coder3101  
> Created_at: 2020-08-05 02:32:54 UTC  
> Updated_at: 2020-08-05 02:34:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-668940988  

Not directly related with the PR but your work.  
  
```cpp  
  template<typename T>   
  struct is_matrix : std::integral_constant<bool, false>{};  
  
  template<typename T>   
  inline static constexpr bool const is_matrix_v = is_matrix<T>::value;  
  
  template<typename ExtentsType, ExtentsType... E>  
  struct is_matrix< basic_static_extents<ExtentsType, E...> > : std::false_type{};  
  
  template<typename ExtentsType, ExtentsType E0, ExtentsType E1, ExtentsType E2, ExtentsType... E>  
  struct is_matrix< basic_static_extents<ExtentsType, E0, E1, E2, E...> >{  
    static constexpr bool const value =   
      is_matrix_v< basic_static_extents<ExtentsType, E0, E1> > &&   
      is_scalar_v< basic_static_extents<ExtentsType, E2, E...> >;  
  };  
  
  template<typename ExtentsType, ExtentsType E0, ExtentsType E1>  
  struct is_matrix< basic_static_extents<ExtentsType, E0, E1> >{  
    static constexpr bool const value = ( E0 > ExtentsType(1) && E1 > ExtentsType(1) );  
  };  
```  
  
I am wondering how can this be useful. Except in determining if a tensor can be converted to a matrix. I have to create a type trait that restricts the static extent to be like `<int, 2,3>`. Your above trait picks up `<int,2,3,1>` as a matrix too, which is  a matrix if squeezed but generally when user is creating a matrix they will write `static_matrix<int, 2, 3>`, so a matrix engine needs to static assert for extents that exceeds rank 2.

---

## Comment 29

> Username: amitsingh19975  
> Created_at: 2020-08-05 12:52:10 UTC  
> Updated_at: 2020-08-06 10:19:14 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669175047  

> Not directly related with the PR but your work.  
>   
> ```c++  
>   template<typename T>   
>   struct is_matrix : std::integral_constant<bool, false>{};  
>   
>   template<typename T>   
>   inline static constexpr bool const is_matrix_v = is_matrix<T>::value;  
>   
>   template<typename ExtentsType, ExtentsType... E>  
>   struct is_matrix< basic_static_extents<ExtentsType, E...> > : std::false_type{};  
>   
>   template<typename ExtentsType, ExtentsType E0, ExtentsType E1, ExtentsType E2, ExtentsType... E>  
>   struct is_matrix< basic_static_extents<ExtentsType, E0, E1, E2, E...> >{  
>     static constexpr bool const value =   
>       is_matrix_v< basic_static_extents<ExtentsType, E0, E1> > &&   
>       is_scalar_v< basic_static_extents<ExtentsType, E2, E...> >;  
>   };  
>   
>   template<typename ExtentsType, ExtentsType E0, ExtentsType E1>  
>   struct is_matrix< basic_static_extents<ExtentsType, E0, E1> >{  
>     static constexpr bool const value = ( E0 > ExtentsType(1) && E1 > ExtentsType(1) );  
>   };  
> ```  
>   
> I am wondering how can this be useful. Except in determining if a tensor can be converted to a matrix. I have to create a type trait that restricts the static extent to be like `<int, 2,3>`. Your above trait picks up `<int,2,3,1>` as a matrix too, which is a matrix if squeezed but generally when user is creating a matrix they will write `static_matrix<int, 2, 3>`, so a matrix engine needs to static assert for extents that exceeds rank 2.  
  
These type traits are not limited to matrix only but you have to generalize for tensor. For example, let's take two tensors of `A(1,3,4,5)` and `B( 4, 3, 4, 1 )` these are not matrices now if we apply tensor times tensors two-dimension will contract   
  
`C(1,4,5,1) = A(1,3,4,5) x B(4, 3, 4, 1)`   
  
The resulting tensor is a matrix if we squeeze otherwise it's a tensor and if the user does not want to squeeze the extents then `static_matrix<int, 4, 5>` will fail.

---

## Comment 30

> Username: coder3101  
> Created_at: 2020-08-05 14:16:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669219587  

So, I will create matrix engine that wants explicitly squeezing into rank 2.

---

## Comment 31

> Username: amitsingh19975  
> Created_at: 2020-08-05 19:52:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669461169  

> So, I will create matrix engine that wants explicitly squeezing into rank 2.  
  
This could be problematic if you implicitly squeeze because the user may not want to squeeze and wants to apply further operations on it. We don't want any implicit operation until the users want to do it. If the user creates the matrix then after applying operation it's up to the user what he/she wants from it.

---

## Comment 32

> Username: coder3101  
> Created_at: 2020-08-05 20:12:27 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669477047  

> > So, I will create matrix engine that wants explicitly squeezing into rank 2.  
>   
> This could be problematic if you implicitly squeeze because the user may not want to squeeze and wants to apply further operations on it. We don't want any implicit operation until the users want to do it. If the user creates the matrix then after applying operation it's up to the user what he/she wants from it.  
  
Sorry if you got it wrong. I mean if user wants to create matrix from a tensor which can be squeezed to rank 2. The user will have to first squeeze the tensor then assign it to matrix. Nothing will be squeezed implicitly.

---

## Comment 33

> Username: amitsingh19975  
> Created_at: 2020-08-05 20:21:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669482361  

> > > So, I will create matrix engine that wants explicitly squeezing into rank 2.  
> >   
> >   
> > This could be problematic if you implicitly squeeze because the user may not want to squeeze and wants to apply further operations on it. We don't want any implicit operation until the users want to do it. If the user creates the matrix then after applying operation it's up to the user what he/she wants from it.  
>   
> Sorry if you got it wrong. I mean if user wants to create matrix from a tensor which can be squeezed to rank 2. The user will have to first squeeze the tensor then assign it to matrix. Nothing will be squeezed implicitly.  
  
ok. We can constraint the dimensions for the matrix to two dimensions if the user wants to assign the tensor to the matrix he will not be able to.  
  
```cpp  
template<typename T>  
using dynamic_matrix = tensor_core<           
      tensor_engine<   
            dynamic_extents<2>,  
            Layout,  
            strides<dynamic_extents<2>>,  
            std::vector< T >  
        >    
>;  
  
template<typename T, size_t N, size_t M>  
using static_matrix = tensor_core<           
      tensor_engine<   
            static_extents<N,M>,  
            Layout,  
            strides<static_extents<N,M>>,  
            std::array< T, N*M >  
        >    
>;  
```

---

## Comment 34

> Username: bassoy  
> Created_at: 2020-08-06 14:57:05 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669980327  

Your last commit https://github.com/boostorg/ublas/pull/90/commits/c2a99ac89e123160dd4cfcd67d8369b787a36c15 includes a change in the evaluation expression. Please exclude that change regarding SIMD because if you introduce too many new features the reviewing process takes too difficult and too long.  
  
Introducing SIMD could is a new feature and should be handled separately with a new feature branch.

---

## Comment 35

> Username: amitsingh19975  
> Created_at: 2020-08-06 14:59:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669981828  

> Your last commit [c2a99ac](https://github.com/boostorg/ublas/commit/c2a99ac89e123160dd4cfcd67d8369b787a36c15) includes a change in the evaluation expression. Please exclude that change regarding SIMD because if you introduce too many new features the reviewing process takes too difficult and too long.  
>   
> Introducing SIMD could is a new feature and should be handled separately with a new feature branch.  
  
ok.

---

## Comment 36

> Username: bassoy  
> Created_at: 2020-08-06 15:03:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-669984116  

>   
>   
> > > > So, I will create matrix engine that wants explicitly squeezing into rank 2.  
> > >   
> > >   
> > > This could be problematic if you implicitly squeeze because the user may not want to squeeze and wants to apply further operations on it. We don't want any implicit operation until the users want to do it. If the user creates the matrix then after applying operation it's up to the user what he/she wants from it.  
> >   
> >   
> > Sorry if you got it wrong. I mean if user wants to create matrix from a tensor which can be squeezed to rank 2. The user will have to first squeeze the tensor then assign it to matrix. Nothing will be squeezed implicitly.  
>   
> ok. We can constraint the dimensions for the matrix to two dimensions if the user wants to assign the tensor to the matrix he will not be able to.  
>   
> ```c++  
> template<typename T>  
> using dynamic_matrix = tensor_core<           
>       tensor_engine<   
>             dynamic_extents<2>,  
>             Layout,  
>             strides<dynamic_extents<2>>,  
>             std::vector< T >  
>         >    
> >;  
>   
> template<typename T, size_t N, size_t M>  
> using static_matrix = tensor_core<           
>       tensor_engine<   
>             static_extents<N,M>,  
>             Layout,  
>             strides<static_extents<N,M>>,  
>             std::array< T, N*M >  
>         >    
> >;  
> ```  
  
I will put this discussion on API in our gitter channel for discussion as this is an important point.

---

## Review 37 [Changes requested]

> Username: bassoy  
> Created_at: 2020-08-18 19:41:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-469745090  

I still cannot approve due to `rebind_storage_t` usage.

---

## Review 38 [Commented]

> Username: coder3101  
> Created_at: 2020-10-02 11:48:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-501067079  

📁 .github/workflows/clangtidy_check.yml

```diff
  25 |+           wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key 2>/dev/null | sudo apt-key add -
  26 |+           sudo add-apt-repository 'deb http://apt.llvm.org/focal llvm-toolchain-focal-10 main' -y
  27 |+           sudo apt-get update -q
```

> Username: coder3101  
> Created_at: 2020-10-02 11:48:22 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498772911  

Doesn't Ubuntu focal already have clang-10 in its repositories?

> Username: amitsingh19975  
> Created_at: 2020-10-02 11:50:53 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498773923  

It was failing with the error and I found a solution over [here](https://github.com/actions/virtual-environments/issues/1536#issuecomment-698537248)

> Username: amitsingh19975  
> Created_at: 2020-10-02 11:54:34 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498775460  

same with the msvc because the newest version of the windows 2019 toolset is 14.27 and clang-6.0 is removed from the image also which I had to edit the script to install it.

> Username: amitsingh19975  
> Created_at: 2020-10-02 11:55:47 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498775964  

I think they updated the image because of which these errors are triggering right now.

> Username: coder3101  
> Created_at: 2020-10-02 11:58:50 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498777188  

Okay, then. It is fine to not depend upon the image vendor. They update it quite regularly (weekly)

> Username: amitsingh19975  
> Created_at: 2020-10-02 12:03:25 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498779061  

could you fix these errors in the main repo? otherwise, if someone commits it will give unnecessary errors.


---

## Review 39 [Commented]

> Username: github-actions[bot]  
> Created_at: 2020-10-02 11:48:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-501067151  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/static_strides.hpp

```diff
 163 |+       "ExtentsType must be type of tensor extents" 
 164 |+     );
 165 |+     using type = decltype( make_static_strides<Layout>(ExtentsType{}) );
```

> Username: github-actions[bot]  
> Created_at: 2020-10-02 11:48:31 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r498772972  

no matching function for call to `make_static_strides` [clang-diagnostic-error]  
```cpp  
using type = decltype( make_static_strides<Layout>(ExtentsType{}) );  
                           ^  
include/boost/numeric/ublas/tensor/static_strides.hpp:170:51: note: in instantiation of template class 'boost::numeric::ublas::detail::strides_helper<boost::numeric::ublas::basic_column_major<unsigned long, long>, boost::numeric::ublas::basic_static_extents<unsigned long, 1, 2, 3, 4, 5> >' requested here  
  inline static constexpr auto strides_helper_v = strides_helper<Layout, ExtentsType>::value;  
                                                  ^  
include/boost/numeric/ublas/tensor/static_strides.hpp:301:52: note: in instantiation of variable template specialization 'boost::numeric::ublas::detail::strides_helper_v' requested here  
  static constexpr base_type const m_data{ detail::strides_helper_v<layout_type,extents_type> };  
                                                   ^  
include/boost/numeric/ublas/tensor/static_strides.hpp:268:12: note: in instantiation of static data member 'boost::numeric::ublas::basic_static_strides<boost::numeric::ublas::basic_static_extents<unsigned long, 1, 2, 3, 4, 5>, boost::numeric::ublas::basic_column_major<unsigned long, long> >::m_data' requested here  
    return m_data.data();  
           ^  
include/boost/numeric/ublas/tensor/tensor_core.hpp:245:47: note: in instantiation of member function 'boost::numeric::ublas::basic_static_strides<boost::numeric::ublas::basic_static_extents<unsigned long, 1, 2, 3, 4, 5>, boost::numeric::ublas::basic_column_major<unsigned long, long> >::data' requested here  
                other.data(), other.strides().data());  
                                              ^  
/home/runner/work/ublas/ublas/examples/tensor/fixed_rank_tensor.cpp:82:41: note: in instantiation of function template specialization 'boost::numeric::ublas::tensor_core<boost::numeric::ublas::tensor_engine<boost::numeric::ublas::basic_fixed_rank_extents<unsigned long, 5>, boost::numeric::ublas::basic_column_major<unsigned long, long>, boost::numeric::ublas::strides<boost::numeric::ublas::basic_fixed_rank_extents<unsigned long, 5> >, std::vector<float, std::allocator<float> > > >::tensor_core<boost::numeric::ublas::tensor_engine<boost::numeric::ublas::basic_static_extents<unsigned long, 1, 2, 3, 4, 5>, boost::numeric::ublas::basic_column_major<unsigned long, long>, boost::numeric::ublas::strides<boost::numeric::ublas::basic_static_extents<unsigned long, 1, 2, 3, 4, 5> >, std::array<float, 120> > >' requested here  
    ub::fixed_rank_tensor<float,5> t4 = st1;  
                                        ^  
include/boost/numeric/ublas/tensor/static_strides.hpp:120:18: note: candidate template ignored: could not match 'static_stride_list' against 'basic_static_extents'  
  constexpr auto make_static_strides( [[maybe_unused]] static_stride_list<T, E0, E...> e )  
                 ^  
include/boost/numeric/ublas/tensor/static_strides.hpp:153:18: note: candidate template ignored: could not match 'static_stride_list' against 'basic_static_extents'  
  constexpr auto make_static_strides( [[maybe_unused]] static_stride_list<T> e )  
                 ^  
```  
Reported as error by clang-tidy.


---

## Review 40 [Changes requested]

> Username: bassoy  
> Created_at: 2020-10-03 15:02:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-501538007  

Great enhancements!  
Only two comments which you can find in the corresponding files.  
Please change the `valid` function to `is_valid` to be consistent with the naming.

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  44 |- } // namespace boost::numeric::ublas
  45 |- 
  18 |+ #include <boost/numeric/ublas/tensor/tensor.hpp>
```

> Username: bassoy  
> Created_at: 2020-10-03 10:53:33 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499137850  

`functions.hpp` should not depend on `tensor.hpp`

> Username: amitsingh19975  
> Created_at: 2020-10-03 15:22:41 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499156772  

I will change it to include `tensor_engine.hpp`, if we don't include it and forward declare it then we won't be able to create the result type because it needs to know how to construct the object. We cannot forward declare a class. There is another way which is including it after defining `tensor_engine` in the same header file or include it after the including `tensor_enging.hpp`.

> Username: bassoy  
> Created_at: 2020-10-03 17:05:44 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499164541  

You are right my mistake - I confused `functions.hpp` with `multiplication.hpp`. I should have named those files differently.  
I would not like to have ordering dependencies. So it is okay like that.

---

📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
  24 |         template<typename E>
  53 |-         constexpr auto extents_result_tensor_times_vector( E const& ){
  25 |+         constexpr auto extents_result_tensor_times_vector( [[maybe_unused]] E const& e){
```

> Username: bassoy  
> Created_at: 2020-10-03 12:09:49 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499142558  

I would like these `extents_result_*` expressions inside the corresponding `prod` functions

> Username: amitsingh19975  
> Created_at: 2020-10-03 16:44:46 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499163017  

Moved inside the `prod` functions using lambda.


📁 include/boost/numeric/ublas/tensor/tensor.hpp

```diff
   8 | //
   9 | //  The authors gratefully acknowledge the support of
  10 |- //  Google and Fraunhofer IOSB, Ettlingen, Germany
```

> Username: bassoy  
> Created_at: 2020-10-03 14:47:26 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499154037  

Fraunhofer IOSB was the initiator of this project, please let us keep it.

> Username: amitsingh19975  
> Created_at: 2020-10-03 15:06:52 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499155528  

This must-have occurred during refactoring. Therefore, I will revert it back.


---

## Comment 41

> Username: amitsingh19975  
> Created_at: 2020-10-03 16:46:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#issuecomment-703131384  

> Great enhancements!  
> Only two comments which you can find in the corresponding files.  
> Please change the `valid` function to `is_valid` to be consistent with the naming.  
  
Changed `valid` to `is_valid`.

---

## Review 42 [Approved]

> Username: bassoy  
> Created_at: 2020-10-03 17:53:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/90#pullrequestreview-501560288  

I think I have three remaining comments. Please look at them and see if there is something you would like to change. Otherwise I approve this.

📁 include/boost/numeric/ublas/tensor/tensor_engine.hpp

```diff
  15 |+ 
  16 |+ #include <boost/numeric/ublas/tensor/tensor_core.hpp>
  17 |+ #include <boost/numeric/ublas/tensor/layout.hpp>
```

> Username: bassoy  
> Created_at: 2020-10-03 17:09:36 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499164880  

Are those two required in this file?

> Username: amitsingh19975  
> Created_at: 2020-10-03 18:06:26 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499169138  

nope, the layout does not need to be required by tensor_engine must have been the remains of the refactoring, I will remove it.


📁 include/boost/numeric/ublas/tensor/functions.hpp

```diff
 150 |+                 return extents_type{ std::move(arr) } ;
 151 |+             }
 152 |+         };
```

> Username: bassoy  
> Created_at: 2020-10-03 17:48:12 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499167698  

Just a minor comment:  
  
```cpp  
if constexpr (is_static_rank_v<extents_type>) {  
  constexpr auto size = std::max(a_extents.size()-1u,2u);  // a_extents.size() should be constexpr  
  auto shape = extents<size>(); // we should make e.g. extents<size>(1) or extents<size>{1}  
  shape.fill(1);  
  return shape;  
}  
else{  
  auto size = std::max(a_extents.size()-1u,2u);  // a_extents.size()   
  auto shape = extents(typename extents_type::base_type(size));  
  shape.fill(1u);  
  return shape;   
}

> Username: bassoy  
> Created_at: 2020-10-03 17:52:08 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499168030  

But just leave it like it is. We can do several PRs based on your changes.

> Username: amitsingh19975  
> Created_at: 2020-10-03 18:02:40 UTC  
> Updated_at: 2020-10-05 09:22:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/90#discussion_r499168806  

1. The problem with `extents<size>(1)` is that it accepts `std::initializer_list` which will hinder or try to match with it. That's why I added the function `fill`.  
  
2. I avoided `a_extents.size()` because of msvc's bugs which I faced last time I used it. I don't know if msvc has improved it's `constexpr` capabilities.


---
