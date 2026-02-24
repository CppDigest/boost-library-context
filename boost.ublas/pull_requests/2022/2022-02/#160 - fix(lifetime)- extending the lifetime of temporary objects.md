# #160 fix(lifetime): extending the lifetime of temporary objects [Open]

> Username: amitsingh19975  
> Created at: 2022-02-09 14:45:25 UTC  
> Updated at: 2022-02-16 19:06:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/160  

The current implementation does not take into account the prvalue that  
creates an issue while storing it. The prvalue is destroyed after the  
end of its scope, and if you try to access it, you are entering into  
the undefined land of C++.  
  
To solve this issue, we employ the trait `std::is_lvalue_reference` to  
determine if need to extend the lifetime or not. If an extension is needed,  
we store the value with the help of universal forwarding. Otherwise, we  
extend the lifetime using `const &`.  
  
Resolves #125, resolves #118

---

## Review 1 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-09 15:34:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-877647603  

Reports by clang tidy

---

## Review 2 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-878720203  

Reports by clang tidy

📁 examples/tensor/simple_expressions.cpp

```diff
  69 |+     // and mixing expression templates with prvalues, rvalues, and lvalues
  70 |+     {
  71 |+       auto G = tensor(shape{3,3}, 3.f);
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:19 UTC  
> Updated_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589427  

3.f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto G = tensor(shape{3,3}, 3.f);  
                                  ^  
```  
Reported as warning by clang-tidy.

---

📁 examples/tensor/simple_expressions.cpp

```diff
  77 |+       std::cout << "E(9)=" << tensor(E_9) << ";" << std::endl << std::endl;
  78 |+ 
  79 |+       auto E_6 = G + 3.f;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:19 UTC  
> Updated_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589431  

3.f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto E_6 = G + 3.f;  
                     ^  
```  
Reported as warning by clang-tidy.

---

📁 examples/tensor/simple_expressions.cpp

```diff
  84 |+       std::cout << "E(6)=" << tensor(E_6) << ";" << std::endl << std::endl;
  85 |+ 
  86 |+       auto four = 4.f;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:19 UTC  
> Updated_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589432  

4.f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto four = 4.f;  
                  ^  
```  
Reported as warning by clang-tidy.

---

📁 examples/tensor/simple_expressions.cpp

```diff
  92 |+       std::cout << "E(10)=" << tensor(E_10) << ";" << std::endl << std::endl;
  93 |+ 
  94 |+       auto E_23 = E_10 + E_10 + tensor(shape{3,3}, 3.f);
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:19 UTC  
> Updated_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589433  

3.f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto E_23 = E_10 + E_10 + tensor(shape{3,3}, 3.f);  
                                                   ^  
```  
Reported as warning by clang-tidy.

---

📁 examples/tensor/simple_expressions.cpp

```diff
  99 |+       std::cout << "E(23)=" << tensor(E_23) << ";" << std::endl << std::endl;
 100 |+ 
 101 |+       auto E_9_7 = tensor(shape{3,3}, 5.4f) + 4.3f;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589437  

5.4f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto E_9_7 = tensor(shape{3,3}, 5.4f) + 4.3f;  
                                      ^  
```  
Reported as warning by clang-tidy.

---

📁 examples/tensor/simple_expressions.cpp

```diff
  99 |+       std::cout << "E(23)=" << tensor(E_23) << ";" << std::endl << std::endl;
 100 |+ 
 101 |+       auto E_9_7 = tensor(shape{3,3}, 5.4f) + 4.3f;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-10 11:44:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r803589438  

4.3f is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
auto E_9_7 = tensor(shape{3,3}, 5.4f) + 4.3f;  
                                              ^  
```  
Reported as warning by clang-tidy.


---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2022-02-10 13:56:37 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#issuecomment-1034951310  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Review 4 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-13 11:12:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-880967445  

Reports by clang tidy

---

## Review 5 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-13 11:58:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-880972083  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
 166 | 
  77 |- 
 167 |+     constexpr auto const& left_expr() const noexcept{ return cast_tensor_exression(el); }
```

> Username: github-actions[bot]  
> Created_at: 2022-02-13 11:58:48 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r805323882  

function `left_expr` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
constexpr auto const& left_expr() const noexcept{ return cast_tensor_exression(el); }  
    ^  
    [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
  77 |- 
 167 |+     constexpr auto const& left_expr() const noexcept{ return cast_tensor_exression(el); }
 168 |+     constexpr auto const& right_expr() const noexcept{ return cast_tensor_exression(er); }
```

> Username: github-actions[bot]  
> Created_at: 2022-02-13 11:58:48 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r805323883  

function `right_expr` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
constexpr auto const& right_expr() const noexcept{ return cast_tensor_exression(er); }  
    ^  
    [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
 149 |-     
 223 |+ 
 224 |+     constexpr auto const& expr() const noexcept{ return cast_tensor_exression(e); }
```

> Username: github-actions[bot]  
> Created_at: 2022-02-13 11:58:48 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r805323885  

function `expr` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
constexpr auto const& expr() const noexcept{ return cast_tensor_exression(e); }  
    ^  
    [[nodiscard]]  
```  
Reported as warning by clang-tidy.


---

## Review 6 [Commented]

> Username: amitsingh19975  
> Created_at: 2022-02-13 12:32:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-880975662  

📁 include/boost/numeric/ublas/tensor/operators_comparison.hpp

```diff
 208 |+ constexpr bool operator==( typename T::value_type lhs, boost::numeric::ublas::detail::tensor_expression<T,D> const& rhs) noexcept{
 209 |     return boost::numeric::ublas::detail::compare( rhs, [lhs](auto const& r){ return lhs == r; } );
 210 | }
```

> Username: amitsingh19975  
> Created_at: 2022-02-13 12:32:32 UTC  
> Updated_at: 2022-02-13 12:33:05 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r805327890  

Should we use the std::move, or are we only expecting the tensor to have standard C++ arithmetic types and don't need to care about other types, even user-defined types?  
```diff  
+constexpr bool operator==( typename T::value_type lhs, boost::numeric::ublas::detail::tensor_expression<T,D> const& rhs) noexcept{  
+    return boost::numeric::ublas::detail::compare( rhs, [lhs = std::move(lhs)](auto const& r){ return lhs == r; } );  
+}  
```


---

## Review 7 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-15 09:13:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-882675783  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
 143 | protected :
 144 |+     constexpr tensor_expression(tensor_expression&&) noexcept = default;
 145 |+     constexpr tensor_expression& operator=(tensor_expression&&) noexcept = default;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 09:13:01 UTC  
> Updated_at: 2022-02-15 09:13:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806608712  

deleted member function should be public [hicpp-use-equals-delete,modernize-use-equals-delete]  
```cpp  
constexpr tensor_expression& operator=(tensor_expression&&) noexcept = default;  
                                 ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
 197 |+ protected:
 198 |+     constexpr binary_tensor_expression(binary_tensor_expression&& l) noexcept = default;
 199 |+     constexpr binary_tensor_expression& operator=(binary_tensor_expression&& l) noexcept = default;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 09:13:01 UTC  
> Updated_at: 2022-02-15 09:13:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806608716  

deleted member function should be public [hicpp-use-equals-delete,modernize-use-equals-delete]  
```cpp  
constexpr binary_tensor_expression& operator=(binary_tensor_expression&& l) noexcept = default;  
                                        ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression.hpp

```diff
 270 |+ protected:
 271 |+     constexpr unary_tensor_expression(unary_tensor_expression&& l) noexcept = default;
 272 |+     constexpr unary_tensor_expression& operator=(unary_tensor_expression&& l) noexcept = default;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 09:13:01 UTC  
> Updated_at: 2022-02-15 09:13:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806608717  

deleted member function should be public [hicpp-use-equals-delete,modernize-use-equals-delete]  
```cpp  
constexpr unary_tensor_expression& operator=(unary_tensor_expression&& l) noexcept = default;  
                                       ^  
```  
Reported as warning by clang-tidy.


---

## Review 8 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/160#pullrequestreview-882910583  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 127 |- 	    return expr.er.extents();
 146 |+ 	if constexpr ( is_tensor_type_v<EL> )
 147 |+ 	    return retrieve_extents(lexpr);
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:28 UTC  
> Updated_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778694  

repeated branch in conditional chain [bugprone-branch-clone]  
```cpp  
return retrieve_extents(lexpr);  
            ^  
include/boost/numeric/ublas/tensor/expression_evaluation.hpp:147:36: note: end of the original  
            return retrieve_extents(lexpr);  
                                          ^  
include/boost/numeric/ublas/tensor/expression_evaluation.hpp:153:6: note: clone 1 starts here  
            return retrieve_extents(lexpr);  
            ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 130 |- 	    return retrieve_extents(expr.el);
 149 |+ 	else if constexpr ( is_tensor_type_v<ER> )
 150 |+ 	    return retrieve_extents(rexpr);
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778697  

repeated branch in conditional chain [bugprone-branch-clone]  
```cpp  
return retrieve_extents(rexpr);  
            ^  
include/boost/numeric/ublas/tensor/expression_evaluation.hpp:150:36: note: end of the original  
            return retrieve_extents(rexpr);  
                                          ^  
include/boost/numeric/ublas/tensor/expression_evaluation.hpp:156:6: note: clone 1 starts here  
            return retrieve_extents(rexpr);  
            ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 191 |+ template<class E, std::size_t ... es>
 192 |+ [[nodiscard]] inline
 193 |+   constexpr auto all_extents_equal(ublas_expression<E> const&, extents<es...> const&) noexcept
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778699  

all parameters should be named in a function [hicpp-named-parameter,readability-named-parameter]  
```cpp  
constexpr auto all_extents_equal(ublas_expression<E> const&, extents<es...> const&) noexcept  
                                                             ^  
                                                              /*unused*/             /*unused*/  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 194 |- 	    return false;
 215 |+     if ( !all_extents_equal(cast_expr, e) )
 216 |+ 		return false;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778705  

redundant boolean literal in conditional return statement [readability-simplify-boolean-expr]  
```cpp  
return false;  
~~~~~~~~~~~~~~~~~~~~~~~^~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 224 |- 	    return false;
 232 |+ 	if( !all_extents_equal(lexpr, e) || !all_extents_equal(rexpr, e) )
 233 |+ 		return false;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778707  

redundant boolean literal in conditional return statement [readability-simplify-boolean-expr]  
```cpp  
return false;  
~~~~~~~~~~~~~~~~~~~~~~~^~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/expression_evaluation.hpp

```diff
 245 |- 	    	return false;
 248 |+ 	if( !all_extents_equal(uexpr, e) )
 249 |+ 		return false;
```

> Username: github-actions[bot]  
> Created_at: 2022-02-15 12:28:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/160#discussion_r806778710  

redundant boolean literal in conditional return statement [readability-simplify-boolean-expr]  
```cpp  
return false;  
~~~~~~~~~~~~~~~~~~~~~~~^~~~~~  
```  
Reported as warning by clang-tidy.


---
