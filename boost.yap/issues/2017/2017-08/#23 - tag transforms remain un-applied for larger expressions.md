# #23 - tag transforms remain un-applied for larger expressions [Closed]

> Username: rgrover  
> Created at: 2017-08-13 04:09:47 UTC  
> Updated at: 2018-06-11 17:16:18 UTC  
> Closed at: 2017-08-30 01:19:21 UTC  
> Url: https://github.com/boostorg/yap/issues/23  

I've struggled trying to write what should have been a simple transformation: computing 'min_size' from an expression tree of lazy `std::array`s. The following is my attempt using tag transforms.   
  
```C++  
#include <boost/yap/algorithm.hpp>  
#include <boost/yap/print.hpp>  
  
using namespace boost::yap;  
  
template <boost::yap::expr_kind Kind, typename Tuple>  
struct lazy_array_expr  
{  
    static const boost::yap::expr_kind kind = Kind;  
  
    Tuple elements;  
  
    BOOST_YAP_USER_BINARY_OPERATOR_MEMBER(plus, ::lazy_array_expr)  
};  
  
struct min_size  
{  
    template <size_t N>  
    auto operator()(boost::yap::terminal_tag, std::array<int, N> const &)  
    {  
//        std::cout << "terminal tag, returning " << N << '\n';  
        return make_terminal<lazy_array_expr, size_t>(std::move(N));  
    }  
  
    auto operator()(plus_tag, size_t N0, size_t N1)  
    {  
//        std::cout << "plus tag (" << N0 << ", " << N1 << ")\n";  
        auto result = std::min(N0, N1);  
        return make_terminal<lazy_array_expr, size_t>(std::move(result));  
    }  
};  
  
int main()  
{  
    auto a1 = make_terminal<lazy_array_expr>(std::array<int, 1>{1});  
    auto a2 = make_terminal<lazy_array_expr>(std::array<int, 2>{2, 2});  
    auto a3 = make_terminal<lazy_array_expr>(std::array<int, 3>{3, 3, 3});  
  
    auto t1 = transform(a1 + a2, min_size{});  
    std::cout << "result: " << boost::yap::value(t1) << '\n'; /* this is computed correctly */  
  
    auto t2 = transform(a1 + a2 + a3, min_size{});  
    print(std::cout, t2); /* why is t2 is left in a state where further tag transforms could have been applied? */  
    auto t3 = transform(t2, min_size{}); /* this should not have been necessary */  
    std::cout << "result: " << boost::yap::value(t3) << '\n';  
}  
```  
  
I find that the second application of this transform (in the sample code above as `t2`) fails to reduce the expression-tree into a single expression. The resulting `t2` is:  
```  
expr<+>  
    term<unsigned long>[=1]  
    term<unsigned long>[=3]  
```  
which should have been further transformed by re-employing `min_size::operator()(plus_tag, size_t N0, size_t N1)`  
  
This stands in contrast with the result of the first application (in the sample code as `t1`) on a smaller tree.  
  
Perhaps the accompanying documentation should explain the process of tree traversal when applying a transformation.

---

## Comment 1

> Username: rgrover  
> Created at: 2017-08-14 10:23:57 UTC  
> Url: https://github.com/boostorg/yap/issues/23#issuecomment-322155190  

The following definition of `min_size` works instead:  
  
```C++  
struct min_size  
{  
    template <size_t N>  
    auto operator()(boost::yap::terminal_tag, std::array<int, N> const &)  
    {  
        return N;  
    }  
  
    template <typename Expr1, typename Expr2>  
    auto operator()(plus_tag, Expr1 const &e1, Expr2 const &e2) {  
        size_t N0 = transform(e1, min_size{});  
        size_t N1 = transform(e2, min_size{});  
        return std::min(N0, N1);  
    }  
};  
```  
  
I wonder if the following is what is going on. At compile time, for the expression   
  
```  
expr<+>  
    expr<+>  
        term<std::array<int, 1ul>>[=<<unprintable-value>>] &  
        term<std::array<int, 2ul>>[=<<unprintable-value>>] &  
    term<std::array<int, 3ul>>[=<<unprintable-value>>] &  
```  
  
the inner `plus_tag` non-terminal matches against `auto operator()(plus_tag, size_t N0, size_t N1)`, but Yap doesn't allow the outer `plus_tag` non-terminal to see that the inner non-terminal will reduce to a `size_t` and allow the outer to match against `auto operator()(plus_tag, size_t N0, size_t N1)`.   
  
Is there a way to fix this? The mechanics of yap::transform may benefit from more documentation.

---

## Comment 2

> Username: tzlaine  
> Created at: 2017-08-16 20:17:47 UTC  
> Url: https://github.com/boostorg/yap/issues/23#issuecomment-322886495  

This works as designed.  And looking at the docs again -- wow!  That's really not explained at all in the documentation.  
  
For a thorough explanation, see my talk about Yap from C++Now in May:  
  
https://www.youtube.com/watch?v=VhIwDxkIsME  
  
Start watching around 16:10, for about 2 minutes.  
  
I'll update the docs with that information soon.  Thanks for pointing this out!

---

## Comment 3

> Username: rgrover  
> Created at: 2017-08-17 05:39:43 UTC  
> Updated at: 2017-08-17 05:44:24 UTC  
> Url: https://github.com/boostorg/yap/issues/23#issuecomment-322973900  

Zach, in your talk you explain that transform() algorithm visits the an expression tree top-down and finds the first node for which the transform-object called using visited node gives a well formed expression; and then it doesn't implicitly recurse any further. This is a very helpful model.   
  
It still took me a while to figure out that there is no back-tracking involved during transform. In other words, transform works by only recursing down the tree, and stops as early as possible. It is the responsibility of the transform object to provide a call-operator overload to match the highest expression node (in its raw form) for which transformation is applicable. Conversely, if the transform is to reduce the tree to a single value, then at least one of the call-operator overloads should match the root of the expression tree in its untransformed form, and that this overload is responsible for propagating the transform recursively, collecting the transformed results, and reducing the resulting hana::tuple into a single returnable value.
