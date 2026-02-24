# #483 Make "v" accessible to derived classes [Merged]

> Username: wgledbetter  
> Created at: 2021-01-06 23:42:35 UTC  
> Updated at: 2021-01-16 16:52:23 UTC  
> Merged at: 2021-01-08 16:30:25 UTC  
> Closed at: 2021-01-08 16:30:25 UTC  
> Url: https://github.com/boostorg/math/pull/483  

I'd like to be able to modify the underlying "v" array inside fvar, partially in response to #446

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-01-06 23:58:24 UTC  
> Url: https://github.com/boostorg/math/pull/483#issuecomment-755786143  

@pulver

---

## Review 2 [Approved]

> Username: pulver  
> Created_at: 2021-01-07 14:16:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/483#pullrequestreview-563518647  

Looks good to me.  
  
Would be interested to see how this is used in practice.

---

## Comment 3

> Username: wgledbetter  
> Created_at: 2021-01-16 16:49:46 UTC  
> Updated_at: 2021-01-16 16:52:23 UTC  
> Url: https://github.com/boostorg/math/pull/483#issuecomment-761595316  

@pulver Here's how I'm using it.  
  
I wrote a class `fvar_editor` so I can shift the elements of the 'v' array. I wanted to be able to create `yPrime` and `yPrimePrime` such that:  
```  
y.derivative(n+2) == yPrime.derivative(n+1) == yPrimePrime.derivative(n)  
```  
  
It's implemented to solve my templated derivative issue from #446 like this:  
  
```  
template <class F>  
struct Derivative {  
  F f;  
  
  template <typename T>  
  T calculate(T x) {  
    if (is_fvar<T>::value) {  
      const int Order = T::order_sum;  
      using Type = typename T::root_type;  
      auto xx = fvar<Type, Order+1>(x);  
      auto yy = f.calculate(xx);  
      T yPrime = fvar_editor<Type, Order>::shiftDeriv(yy, 1);  
    } else {  
      auto xx = make_fvar<T, 1>(x);  
      auto yy = f.calculate(xx);  
      T yPrime = yy.derivative(1);  
      return yPrime;  
    }  
  }  
};  
```  
for any class F that has a templated `calculate` method.  
  
This allows you to nest: `Derivative<Derivative<Derivative<...F...>>>`

---
