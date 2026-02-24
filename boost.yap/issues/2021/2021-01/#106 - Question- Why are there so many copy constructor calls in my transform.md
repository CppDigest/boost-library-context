# #106 - Question: Why are there so many copy constructor calls in my transform? [Closed]

> Username: joergbrech  
> Created at: 2021-01-07 09:34:42 UTC  
> Updated at: 2021-01-09 21:43:10 UTC  
> Closed at: 2021-01-09 21:43:10 UTC  
> Url: https://github.com/boostorg/yap/issues/106  

Consider the following (questionable) transform, that replaces `+` with `-`:  
  
```cpp  
// custom expression  
template <boost::yap::expr_kind Kind, typename Tuple>  
struct my_expr  
{  
    inline static const boost::yap::expr_kind kind = Kind;  
    Tuple elements;  
};  
BOOST_YAP_USER_BINARY_OPERATOR(plus, my_expr, my_expr)  
BOOST_YAP_USER_BINARY_OPERATOR(minus, my_expr, my_expr)  
  
// a transform for my custom expression  
struct plus_to_minus_transform  
{  
    template <typename L, typename R>  
    constexpr decltype(auto) operator()(boost::yap::expr_tag<boost::yap::expr_kind::plus>,  
                                        L&& l, R&& r)  
    {  
        return boost::yap::make_expression<my_expr, boost::yap::expr_kind::minus>(  
            boost::yap::transform(boost::yap::as_expr<my_expr>(std::forward<L>(l)), *this),  
            boost::yap::transform(boost::yap::as_expr<my_expr>(std::forward<R>(r)), *this)  
        );  
    }  
};  
```  
  
If I call   
  
```cpp  
auto x = boost::yap::make_terminal<my_expr>(1.);  
auto y = boost::yap::make_terminal<my_expr>(2.);  
auto test = boost::yap::transform(x+y, plus_to_minus_transform{});  
```  
  
both terminals `x` and `y` get copied 5 times during the transform. Intuitively I would think, that no copies are neccessary: I construct new `minus` expressions that I would expect to reference the existing terminals `x` and `y`.  
  
If I do the same for a sum of three terminals  
  
```cpp  
auto x = boost::yap::make_terminal<my_expr>(1.);  
auto y = boost::yap::make_terminal<my_expr>(2.);  
auto y = boost::yap::make_terminal<my_expr>(3.);  
auto test = boost::yap::transform(x+y+z, plus_to_minus_transform{});  
```  
  
I get a total of 28 copy-constructor calls.  
  
## Live Code Example  
  
Here is a [godbold example](https://godbolt.org/z/316qxG) that traces the copy constructor calls.  
  
## Background   
  
Replacing `+` with `-` is useless, my real use case is implementing a caching expression. My strategy is to wrap an expression in something derived from `my_expr<boost::yap::expr_kind::if_else, SomeAppropriateHanaTuple>` and store the cached evaluation result in an `std::optional` after the first evaluation. The `if_else` is used to check wether the cache has a value, retrieve the value if it does and evaluate the wrapped expression if it doesn't. I managed to implement this and it seems to work quite nicely.  
  
Now I wanted to implement a transform that replaces any non-terminal expression with a cached version. Here I have performance issues and during debugging I noticed that I am creating a lot of copies.

---

## Comment 1

> Username: joergbrech  
> Created at: 2021-01-09 21:43:10 UTC  
> Url: https://github.com/boostorg/yap/issues/106#issuecomment-757371583  

Ok, I figured it out. It turns out that if I add a custom copy-constructor to my expression `my_expr` *(I need one for my cached expression, and I used it in the example to std::cout something with each copy-ctor call)*, then there will be no implicitly defined non-trivial move constructor: https://en.cppreference.com/w/cpp/language/move_constructor  
  
So in order to fix my example I need to write a custom move constructor, or explicitly add `my_expr(my_expr&&) = default`: [Live Code](https://godbolt.org/z/4G5a7s)  
  
Sorry for bothering you with this here, it took me a while to spot this one 😅
