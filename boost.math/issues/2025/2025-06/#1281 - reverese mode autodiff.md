# #1281 - reverese mode autodiff [Open]

> Username: demroz  
> Created at: 2025-06-23 14:30:11 UTC  
> Updated at: 2025-08-14 11:28:53 UTC  
> Url: https://github.com/boostorg/math/issues/1281  

hi all,  
I've been working on a bit of a side project ml library and noticed that boost doesn't have a reverse mode autodiff implemented. I have an implementation that works (see https://github.com/demroz/mznn/tree/main/aad). I'd be interested in porting it to boost. Would there be any interest in this?

---

## Comment 1

> Username: mborland  
> Created at: 2025-06-23 16:04:19 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-2997050974  

Yes, I think this could be an interesting addition to the library. Feel free to ping me if you have questions

---

## Comment 2

> Username: demroz  
> Created at: 2025-08-06 12:54:14 UTC  
> Updated at: 2025-08-06 12:55:18 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3160050736  

Hello @mborland   
  
I've made a fork of the library with a mostly working version of reverse mode autodiff:  
https://github.com/demroz/math/blob/develop/include/boost/math/differentiation/autodiff_reverse.hpp  
  
its got a bunch of unit tests testing most functions up to the 4th derivative, and a linear regression example under example:  
https://github.com/demroz/math/blob/develop/example/reverse_mode_linear_regression_example.cpp  
  
I was wondering if I could get someone to take a look at the current library structure and let me know if you have any thoughts about the design and implementation before I continue any further.  
  
One of the main things I'm concerned about is it currently heavily relies on expression templates, which don't seem to play nicely with some boost functions. For example sin_pi  
  
```cpp  
     rvar<T,1,> x = 1.0;  
     boost::math::sin_pi(x); // this is fine  
     boost::math::sin_pi(2.0*x); // this is not, because sin_pi is no longer templated on rvar, but an expression template  
```  
  
This is because with my current implementation all the mathematical overloads are expressions, so:  
``` cpp  
rvar<T,1> x = 1.0;  
auto y = 2.0*x; // y is actually a mult_const_expression<rvar<T,1>>  
```  
  
I can of coarse rewrite a lot of the functions to not depend on the use of expression templates, but the library would take a performance hit. I could also provide overloads for all the special functions boost support in either the reverse mode autodiff namespace or boost namespace. Would love to hear your thoughts on this.

---

## Comment 3

> Username: mborland  
> Created at: 2025-08-06 13:49:13 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3160260863  

Couple comments:  
  
Replace: `#define BUFFER_SIZE 65536` with a `static constexpr std::size_t buffer_size = 65536U;` in your namespace. Any macro will get propagated. If you absolutely need a macro use the form `BOOST_MATH_XXX` as the naming convention.   
  
All of your calls to `assert` replace with `BOOST_MATH_ASSERT`.   
  
You have a several calls to `new`, but no calls to `delete`. Since you have your own `emplace_back` methods tracking the size of the objects you should be able to delete everything in the destructor.  
  
The calls to `boost::math::atanh` or other complex functions can be replaced with `<complex>`. They are legacy and should be throwing deprecation warnings.   
  
Replace `thread_local` with `BOOST_MATH_THREAD_LOCAL`. Also beware that you may run into issues with MinGW if you absolutely need this: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L653. If you do it's entirely fine to run things single threaded on MinGW so as to not hurt the rest of the library.  
  
Looking through this your code is readable and structured well. No complaints there. For your functions like `boost::math::sin_pi` is the way a user interacts with it the same as they would for forward differentiation? I think for ease of use we shouldn't have two different methods.   
  
@pulver may be able to offer you comments. He's the one that wrote all the existing autodiff methods. I believe @NAThompson also has a few opinions.   
  
CC: @jzmaddock and @ckormanyos

---

## Comment 4

> Username: demroz  
> Created at: 2025-08-06 16:27:26 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3160820044  

Thanks for your comments, I'll add your suggestions.  
  
as far as boost functions, I tried to keep the user interface somewhat similar, but in short no. With reverse mode autodiff you differentiate with respect to your entire computational graph not just locally, so you get first order derivatives in constant time for R^n -> R^1 functions. Higher order derivatives aren't necessarily practical for reverse mode, and for high orders forward mode is generally preferable. So the interface I have is something like this:  
  
```cpp  
T f(const T& x, const T& y)  
{  
    auto y1 = log(1 + abs(x)) * exp(y);   
    auto y2 = pow(x + y, 2.5);  
    auto y3 = sqrt(1 + x * y);  
    return y1 + y2 - y3;  
}  
  
rvar<T,1> x = 1.0;  
rvar<T,1> y = 2.0;  
rvar<T,1> z = f(x,y);  
z.backward(); // computes all gradients  
x.adjoint(); // dz/dx  
y.adjoint(); // dz/dy  
/* or for higher order derivatives */  
rvar<T,4> x = 1.0;  
rvar<T,4> y = 2.0;  
rvar<T,4> z = f(x,y);  
auto grad_nd<1>(z, &x, &y); // vector -> dz/dx, dz/y  
auto grad_nd<2>(z,&x,&y); // vector<vector> -> {{d^2 z /dx^2, d^2z/dxdy},{d^2 z /dydx, d^2 z/ dy^2}}  
```  
this interface is probably more similar to something like pytorch or tensorflow, but its fairly standard for reverse mode autodiff. I'm open to input though.  
  
The issue I'm having right now with boost implemented functions is with the fact that I heavily rely on expression templates to define my math overloads. If we dont want to have two versions of these functions in different namespaces, is it possible to overload them inside the boost::math namespace for my expression type? If thats not desired then I think I'll have to remove the expression template gymnastics and take the performance hit.

---

## Comment 5

> Username: mborland  
> Created at: 2025-08-06 16:42:17 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3160860318  

Thanks for the explanation. If you're following standard practice then we're probably fine.  
  
I think it's ok if you have a second version of a function in your own namespace. You could inject them into the `boost::math::differentiation` namespace to match what autodiff does: https://github.com/boostorg/math/blob/develop/example/autodiff_black_scholes.cpp#L11

---

## Comment 6

> Username: demroz  
> Created at: 2025-08-14 11:18:38 UTC  
> Updated at: 2025-08-14 11:22:47 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3188087485  

Hello @mborland   
I'm at a point where I'm just about done with all the stl overloads for the library. I've been working on getting all the boost special functions to work correctly with autodiff. It seems like the way to do it is to implement an expression struct and function overload for every special function boost supports (i.e. see https://github.com/demroz/math/blob/add_special_function_overloads/include/boost/math/differentiation/detail/reverse_mode_autodiff_gamma_function_overloads.hpp for the work I've done for gamma functions). As you can tell, this solution gets quickly out of hand with the amount of boilerplate needed. I have a solution in mind that involves writing a python script that autogenerate all the header files/tests needed to support all the special functions. Though this will take a while and I'd like to get started on writing the docs.  
  
For now I was wondering if there is a list of functions that 100% needed to be supported by the library before I submit a PR? The library has a few thousand lines of code at this point and I'm guessing probably take some time to get reviewed. Currently, I support all the stl ops, including all the trig, trig inverses, hyperbolic trig functions, errf, etc... I don't believe that the forward mode library supports every single special function (at least digging through the tests, I don't test all the special functions being tested)

---

## Comment 7

> Username: mborland  
> Created at: 2025-08-14 11:28:53 UTC  
> Url: https://github.com/boostorg/math/issues/1281#issuecomment-3188114525  

No, there's no defined requirement on what you need to support. If you already support everything the STL does that's a good starting point. In the future you can add more if you so choose, or if we get a bug/feature request from a user specifically looking for additional support.
