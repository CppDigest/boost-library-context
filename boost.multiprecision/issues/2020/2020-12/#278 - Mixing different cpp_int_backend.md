# #278 - Mixing different cpp_int_backend [Closed]

> Username: mglisse  
> Created at: 2020-12-20 23:04:47 UTC  
> Updated at: 2021-01-11 09:48:42 UTC  
> Closed at: 2021-01-11 09:48:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278  

The following program  
```c++  
#include <boost/multiprecision/cpp_int.hpp>  
  
typedef boost::multiprecision::cpp_int T;  
typedef boost::multiprecision::number<boost::multiprecision::cpp_int_backend<512>> U;  
  
int main(){  
  T a = T(1) << 150;  
  T b = T(1) << 150;  
  U c = a * b;  
}  
```  
causes 3 allocations. 2 are normal, for a and b. However, when evaluating c, `a*b` is first evaluated in type T (with an allocation) and then copied to type U, instead of being directly computed in an object of type U. If I naively edit boost to make it call `do_assign(true_)`, and extend `setup_karatsuba` and `multiply_karatsuba` to allow mixed types, it seems to work as expected. It would be useful to be able to mix those types without paying such a penalty.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-21 15:56:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-749046670  

There's a couple of bugs, a big hole in our testing, and some documentation errors here... leave it with me.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-12-26 15:55:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751368879  

See https://github.com/boostorg/multiprecision/pull/279.

---

## Comment 3

> Username: mglisse  
> Created at: 2020-12-26 17:11:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751375745  

Thanks, that seems to help a bit. I assume it is just the first step, I am still getting 3 allocations for the testcase. I guess another important step would be to relax `is_same` in `number::operator=(expression)` (and possibly adapt a few places?) so that it is possible to evaluate an expression directly into a variable of a slightly different type. But that may require some mechanism to advertise what types are compatible enough...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-12-26 18:06:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751381257  

3 allocations in the test case is as low as it will ever get IMO: as there are 3 independent variables each with their own unique value (or am I missing something)?  The move occurs when the result of `a*b` is moved into `c`.  Previously this test case actually had 4 allocations.  
  
Note you can also perform mixed arithmetic via:  
  
```  
multiply(c, a, b);  
```  
  
Which calculates `a*b` at the precision of `c` (and without a temporary).

---

## Comment 5

> Username: mglisse  
> Created at: 2020-12-26 18:30:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751383504  

> Previously this test case actually had 4 allocations.  
  
Only 3, because U has a large enough minBits that it does not require an allocation. That's actually the whole point of using U here.  
  
Yes, `U c;multiply(c,a,b);` works with your branch and has only 2 allocations. But the point of expression templates is to make natural code like `U c=a*b;` evaluate the same as whatever is best, probably `U c;multiply(c,a,b);` indeed in this case. I understand this may not be trivial, but it doesn't seem impossible either.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-12-26 19:10:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751386840  

Ah yes I see.  
  
That's really hard to do in the general case for arbitrary complex expressions.  The number of pairs of types for which it can be applied is really quite small as well as they need to be of equivalent precision (or actually that the precision of the result is <= precision of the arguments) otherwise the semantic change.  
  
There is also a more prosaic problem: I would need roughly twice as much code for the expression template unpacking in order to handle this.  If I can write a suitably smart traits class (introspects to verify that a mixed precision operation is possible), *and* we restrict this to trivial expressions then something might be possible though.

---

## Comment 7

> Username: mglisse  
> Created at: 2020-12-26 22:57:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/278#issuecomment-751404479  

I was hoping that something simple like replacing the `is_same` test in `operator=` would already go a long way, it seemed to help in a simple example, but I didn't test it much, it is possible that the consequences indeed require a lot more code changes / duplication.  
  
My motivation is that I would like to use 2 integer types, cpp_int by default, and U (as defined above, or something similar) for temporaries. I would introduce a `temporary_type` typedef in `number`, based on something in the backend and defaulting to `self_type`, and where we currently have `self_type temp(e);` or `result_type t(a);` inside boost I would use `temporary_type` instead. I might also use that temporary_type in user code. In a small experiment I did (a hack handling just this operation and probably breaking other stuff), it allowed an expression like `a*b<c*d` to be evaluated without allocation as long as the products fit in U (the variables were `cpp_int` and the products did not fit in cpp_int without allocation).
