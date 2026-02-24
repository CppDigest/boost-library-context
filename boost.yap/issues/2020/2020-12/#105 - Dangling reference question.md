# #105 - Dangling reference question [Closed]

> Username: joergbrech  
> Created at: 2020-12-16 10:27:01 UTC  
> Updated at: 2021-01-07 07:44:40 UTC  
> Closed at: 2021-01-07 07:44:40 UTC  
> Url: https://github.com/boostorg/yap/issues/105  

I just found out about `yap` and I am excited about it! This looks like a really useful tool.  As a yap newbie *(who has read the [How Expression Operands Are Treated](https://www.boost.org/doc/libs/1_74_0/doc/html/boost_yap/manual.html#boost_yap.manual.how_expression_operands_are_treated) chapter of the manual)*, I have yet another dangling reference question.   
  
Consider the following [code](https://godbolt.org/z/d7e8a5), that creates a dangling reference to `alpha` in the function body of `Foo`:  
  
```cpp  
#include <iostream>  
#include <boost/yap/expression.hpp>  
#include <boost/yap/print.hpp>  
  
template <typename T>  
using term = boost::yap::terminal<boost::yap::expression, T>;  
using Scalar = term<double>;  
  
auto Foo(Scalar& x, Scalar& y){  
    //Scalar alpha = 0.5; // Wrong, but caught by -fsanitize=address  
    double alpha = 0.5;   // Also wrong, not caught by -fsanitize=address  
    return (1-alpha)*x + alpha*y;  
};  
  
int main ()  
{  
    Scalar x = 2;  
    Scalar y = 1;  
    auto z = Foo(x,y);  
      
    std::cout<<"z = "<<evaluate(z)<<" (should be 1.5)"<<std::endl;  
    boost::yap::print(std::cout, z);  
  
    return 0;  
}  
```  
  
Now you mention in the documentation that I can `std::move` such scoped objects to cast them to rvalue references. But I am really reluctant to move from `alpha` twice. What if `alpha` is the result of a more complex (yap)-expression? Wouldn't that give me undefined behavior at the least? Is there an elegant way to fix this **without** creating two copies of `alpha` and `std::move` each into either of the two subexpressions `1-alpha` and `alpha*y`? This kind of feels like `1-alpha` and `alpha*y` should take up shared ownership of `alpha`, but I am a little worried that an expression template with `shared_ptr`s to operands is a performance killer.  
  
PS: I asked [this question on Stackoverflow](https://stackoverflow.com/questions/65304950/shared-ownership-of-existing-ressource-programming-pattern) in general for expression templates unrelated to `yap`, and one of the comments pointed me in the direction of `boost::proto`/`boost::yap`. At the same place it is suggested that I can use an *AST function* and *placeholders* to fix my ownership issue, but as a complete newbie to `yap` (and also ETs), I have trouble understanding what any of that means. 😅

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-12-17 02:08:16 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-747157289  

You're returning a YAP-expression from `Foo()`, and since YAP-expressions capture lvalues by reference, you're baking an lvalue reference to `alpha` into your return value.  
  
You could fix this by making the captured `alpha`s rvalues: `return (1-std::move(alpha))*x + std::move(alpha)*y`.  
  
This is fine, since `alpha` is a `double` or a `term<double>`.  As you note, this may not work in other code that has a value for whose type move is not the same as copy.  For those, you may want to create a YAP expression that includes a placeholder:  
  
```c++  
auto Foo(Scalar& x, Scalar& y){  
    return (1-yap::_1)*x + yap::_1*y;  
};  
  
int main ()  
{  
    Scalar x = 2;  
    Scalar y = 1;  
    auto z = Foo(x,y);  
      
    std::cout<<"z = "<<evaluate(z, 0.5)<<" (should be 1.5)"<<std::endl;  
    boost::yap::print(std::cout, z);  
  
    return 0;  
}  
```  
  
This allows you to use an rvalue, as I've shown here with `0.5`, *or* an lvalue.  
  
Does that clarify things?

---

## Comment 2

> Username: joergbrech  
> Created at: 2020-12-17 03:57:57 UTC  
> Updated at: 2020-12-17 13:41:08 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-747189176  

Thanks a lot for the quick reply! Reading your answer just made me realize that I phrased my question wrongly, I oversimplified and should've provided more background info on what I am trying to achieve.   
  
My naive idea as an expression template newcomer is that I can take a non ET-implementation of some algorithm and  
 - infest it with ETs, that give me lazy evaluation of that algorithm, together with maybe caching, automatic differentiation or whatever it is the ETs do,  
 - have a switch between the ET- and non ET-version of that algorithm for best performance in a given situation, e.g. dependent on wether I need derivatives or not. If you look at [this code example](https://godbolt.org/z/d7e8a5), there is a macro definition that let's you switch between `double`s and `term<double>`s. Another option would be to make the `Foo` function a template.  
  
To evaluate if ETs/yap is the way to go for me, I chose the following algorithm as a particularly challenging testpiece *(and also because I want lazy evaluation, caching and automatic differentiation in the domain of CAD)*:  
  
```cpp  
using Scalar = ... // could be double, could be term<double>  
using Pnt3 = ... // could be an expression of a collection, a collection of expressions or a valarray of doubles, or an Eigen::VectorXf, or ...  
  
struct BSpline  
{  
    std::vector<Scalar> m_knots;  
    std::vector<Pnt3> m_control_points;  
  
    Something de_boor(Scalar& param, size_t k, size_t i){  
  
        if( k == 0)  
            return m_control_points[i];  
        else  
        {  
            auto alpha = (param - m_knots[i]) / (m_knots[ i + m_degree + 1 - k] - m_knots[i]);  
            return (1.0 - alpha)*de_boor(param, k-1, i-1) + alpha*de_boor(param, k-1, i);  
        }  
    }  
}  
```  
  
Here, `Something` must be something that evaluates to a Point. I always get hung up on ownership issues with `alpha` with this example. The `Foo` of my original question was just an oversimplified version of the `de_boor` algorithm. I don't know if I can really use placeholders because of the recursive nature of the algorithm.

---

## Comment 3

> Username: tzlaine  
> Created at: 2020-12-18 01:57:12 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-747817745  

Well, you can just directly convert that from what you wrote to a YAP expression.  Whether or not that chokes your compiler will depend on your top-level value of `k` I think.  
  
You might want to consider exactly where the laziness should be introduced.  It may be possible to make only part of the return expression in the non-base case a YAP expression, which may help with compile times if that's an issue.  
  
Clearly, the base case return must be a YAP expression.  
  
Tho make that function work, `k` needs to be made a template parameter, and your `if` needs to become `if constexpr`.   As long as `de_boor()` returns something that models the YAP expression concept, you should get a result that you can use with YAP (for example, by passing it to `evaluate()`).  
  
That should get you pretty close; the rest is left as an exercise for the reader. :)

---

## Comment 4

> Username: joergbrech  
> Created at: 2020-12-18 07:01:16 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-747909534  

Really? Without dangling references to the `alpha`s? :thinking: Ok, I will give it a go, thanks! :rocket:   
  
FYI, `k` is the degree of the b-spline and typically 3 or less. Ideally, I would have `param`, and each knot and control point a terminal. I like the idea of making `k` a template and using `if constexpr`!

---

## Comment 5

> Username: tzlaine  
> Created at: 2020-12-18 17:29:39 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-748219414  

> Really? Without dangling references to the `alpha`s? 🤔 Ok, I will give it a go, thanks! 🚀  
  
Now, I didn't say that.  I just didn't mention it again because we addressed that issue earlier.

---

## Comment 6

> Username: joergbrech  
> Created at: 2021-01-07 07:44:40 UTC  
> Url: https://github.com/boostorg/yap/issues/105#issuecomment-755946164  

Ok, so an easy fix to solve my problem is to add a `linear_combine` function and wrap the return statement of `Foo` *(respectively `de_boor`)* in an `#ifdef` like so:  
  
```cpp  
double linear_combine(double alpha, double& x, double& y)  
{  
    return (1-alpha)*x + alpha*y;  
}  
  
auto Foo(Scalar& x, Scalar& y){  
    Scalar alpha = 0.5; // or some more complex calculation  
#ifdef USE_YAP  
    return boost::yap::make_terminal(linear_combine)(std::move(alpha), x, y);  
#else  
    return linear_combine(alpha, x, y);  
#endif  
}  
```  
  
[Live Code](https://godbolt.org/z/ancf4c)  
  
Sorry, I should have spotted this earlier myself. Thanks for the help though!
