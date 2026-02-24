# #446 - Initializing nested autodiff::fvar [Closed]

> Username: wgledbetter  
> Created at: 2020-10-27 22:55:40 UTC  
> Updated at: 2020-10-28 16:49:05 UTC  
> Closed at: 2020-10-28 16:49:04 UTC  
> Url: https://github.com/boostorg/math/issues/446  

This might just be me misunderstanding how the `fvar` type is meant to be used.  
  
This code works:  
```  
fvar<double, 1> x1 = make_fvar<double, 1>(1.5);  
auto y1 = myFunction(x1);  
cout << "y1 deriv = " << y1.derivative(1) << endl;  
  
// Output:  
    y1 deriv = 12.345  
```  
  
I would like to be able to do:  
```  
fvar<fvar<double, 1>, 1> x2 = make_fvar<fvar<double, 1>, 1>(1.5);  
auto y2 = myFunction(x2);  
cout << "y2 deriv = " << y2.derivative(1) << endl;  
  
// Output:  
    y2 deriv = depth(1)(12.345, 98.765)  
```  
However, `make_fvar<fvar<double, 1>, 1>()` throws a compile-time error that there is no matching constructor. Reading further, it is trying to convert from `const fvar<double,1>` to `const double`. I understand that if you down-cast you would lose the derivative information, but would it make sense to have a workaround in this case?  
  
Alternatively, am I missing something?

---

## Comment 1

> Username: pulver  
> Created at: 2020-10-28 11:56:11 UTC  
> Url: https://github.com/boostorg/math/issues/446#issuecomment-717884794  

What is it that you are trying to do, mathematically?  
  
It looks like you are trying to declare `x2` to be an independent variable of differentiation from `x1`. If that is the case, then here are a few ways to do this (but only the last 2 ways are recommended.)  
  
### Method 1 (Not recommended - don't call fvar directly)  
  
```  
#include <boost/math/differentiation/autodiff.hpp>  
#include <iostream>  
  
using namespace boost::math::differentiation;  
  
template <typename RealType, size_t Order>  
using fvar = detail::fvar<RealType, Order>;  
  
auto myFunction = [](auto x1, auto x2) {  
  return x1*x1 + x2*x2;  
};  
  
int main() {  
  fvar<double, 1> x1 = fvar<double, 1>(2, true);  
  fvar<fvar<double, 1>, 1> x2 = fvar<fvar<double, 1>, 1>(3, true);  
  auto y2 = myFunction(x1, x2);  
  
  std::cout << "y2.derivative(0,0) = " << y2.derivative(0,0) << std::endl; // 13  
  std::cout << "y2.derivative(0,1) = " << y2.derivative(0,1) << std::endl; // 6  
  std::cout << "y2.derivative(1,0) = " << y2.derivative(1,0) << std::endl; // 4  
  std::cout << "y2.derivative(1,1) = " << y2.derivative(1,1) << std::endl; // 0  
  return 0;  
}  
```  
`fvar` is generally meant to only be used internally, and should not normally need to be used directly. This type is generally hidden by `auto` declarations and template parameters.  
  
### Method 2 (Use make_ftuple)  
  
The above method is clumsy with the nested `fvar`s. This is what `make_ftuple` was designed for. It allows you to declare all the independent variables you need for the calculation at once and returns them into a single `std::tuple`:  
  
```  
int main() {  
  auto vars = make_ftuple<double, 1, 1>(2, 3);  
  auto& x1 = std::get<0>(vars);  
  auto& x2 = std::get<1>(vars);  
  auto y2 = myFunction(x1, x2);  
  ...  
}  
```  
  
### Method 3 (make_ftuple + std::apply)  
  
C++17's `std::apply` allows you to invoke a generic lambda with the tuple as parameters:  
  
```  
  auto vars = make_ftuple<double, 1, 1>(2, 3);  
  auto y2 = std::apply(myFunction, vars);  
  ...  
```  
If `myFunction` is a function template, then use Method 2 instead.  
  
Another example is [here](https://www.boost.org/doc/libs/develop/libs/math/doc/html/math_toolkit/autodiff.html#math_toolkit.autodiff.example-multiprecision).

---

## Comment 2

> Username: wgledbetter  
> Created at: 2020-10-28 15:35:48 UTC  
> Url: https://github.com/boostorg/math/issues/446#issuecomment-718016546  

Here's the full context of what I'm trying to do.  
  
I'd like to have this `Derivative` class that takes the derivative of a function `F` that's defined in `F::calculate()`.  
```  
template <class F>  
struct Derivative {  
  F f;  
  
  template <typename T>  
  T calculate(T x) {  
    auto xx = make_fvar<T, 1>(x);  
    auto yy = f.calculate(xx);  
    T yPrime = yy.derivative(1);  
    return yPrime;  
  }  
};  
```  
Here's an arbitrary function definition.  
```  
struct Function {  
  template <typename T>  
  T calculate(T x) {  
    // Some function body  
    using std::sin;  
    return x*x + x + sin(x);  
  }  
};  
```  
Now, because everything is templated, and the interface `calculate` is the same, I'd like it to support `Derivative< Derivative<Function> >`, or even `Derivative< AnotherFunction< Derivative<Function> > >`.  
```  
int main() {  
  double x = 12.345;  
  
  Function f;  
  double y = f.calculate(x);  
  
  // First derivative works  
  Derivative<Function> d;  
  double yp = d.calculate(x);  
  
  // Second derivative does not compile because make_fvar is asked to create fvar<fvar<double, 1>, 1>  
  Derivative<Derivative<Function>> dd;  
  double ypp = dd.calculate(x);  
}  
```  
  
Thanks for the detailed reply.

---

## Comment 3

> Username: pulver  
> Created at: 2020-10-28 16:07:26 UTC  
> Updated at: 2020-10-28 16:08:46 UTC  
> Url: https://github.com/boostorg/math/issues/446#issuecomment-718038325  

The composition `Derivative<Derivative<F>>` should somehow result in the `1` becoming a `2` in:  
```  
auto xx = make_fvar<T, 2>(x);  
auto yy = f.calculate(xx);  
T yPrime = yy.derivative(2);  
```  
Consider a template parameter that counts the depth of the `Derivative<T>` nesting, and use that in place of `2`. Instead of nesting `fvar<>` types, it should increment the `Order`.  
  
In contrast, you don't want to nest `fvar<>` types with itself since this is precisely how independent variable types are constructed/represented in `autodiff`.  
  
Whether this fits in with the more general paradigm you're describing I'm not sure, but hope that helps.

---

## Comment 4

> Username: wgledbetter  
> Created at: 2020-10-28 16:49:04 UTC  
> Url: https://github.com/boostorg/math/issues/446#issuecomment-718066063  

Thanks. That answers my main question. I was hoping to avoid that approach, but I'll play around with it. It seems like it wouldn't be able to handle something like   
![equation](https://latex.codecogs.com/png.latex?%5Cfrac%7B%5Cpartial%7D%7B%5Cpartial%20x%7D%5Cleft%5B%20g%28x%2C%20f%28x%29%2C%20f%27%28x%29%29%20%5Cright%20%5D)  
where `f'(x)` is calculated with `Derivative`, but I'll comment again if I get a solution.
