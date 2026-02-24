# #157 - `formula_format` only defined in .cpp file [Closed]

> Username: NAThompson  
> Created at: 2018-11-14 19:49:32 UTC  
> Updated at: 2019-09-14 15:57:44 UTC  
> Closed at: 2019-09-14 15:57:44 UTC  
> Url: https://github.com/boostorg/math/issues/157  

In the [polynomial documentation](https://www.boost.org/doc/libs/release/libs/math/doc/html/math_toolkit/polynomials.html), a very nice function called `formula_format` is discussed. However, grepping through the project, the only place I can find the definition of this function is in `example/polynomial_arithmetic.cpp`, and hence I can't `#include` it anywhere in my own code.  
  
This `formula_format` should be made a member function on the `polynomial` class, or a free function somewhere accessible.

---

## Comment 1

> Username: ilazaric  
> Created at: 2019-02-12 21:37:34 UTC  
> Url: https://github.com/boostorg/math/issues/157#issuecomment-462945764  

The implementation of `formula_format` in `example/polynomial_arithmetic.cpp` does not work with  
`polynomial<std::complex<double>>` (it uses comparison). Also, it probably should not return a `string`, but a wrapper `polynomial_pretty<T>`, so it can access the stream manipulators.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-09-14 15:32:57 UTC  
> Url: https://github.com/boostorg/math/issues/157#issuecomment-531488947  

Just catching up on these old issues.... :)  
  
I can't help wondering if what we really need is a proper pretty-formatting library for numerical data.  Complete with multiple output formats (text, html, Tex, Docbook etc), plus multiple input formats (number, complex, interval, polynomial, array etc) each of which builds simpler constructs below it.  
  
It's not something I want to take on right now, but I do sort of like the idea (and it would be pretty darn useful).

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-09-14 15:57:44 UTC  
> Url: https://github.com/boostorg/math/issues/157#issuecomment-531490920  

@jzmaddock : I think you're onto something here; that'd be a huge improvement. For now, I'm personally unable to contribute to that effort, and in addition, it might be good to backburner it until the C++ committee decides on what the right way to do unicode is. Then we could use subscripts/superscripts and so on.
