# #79 - tap - incoherency [Closed]

> Username: viboes  
> Created at: 2015-10-05 23:47:48 UTC  
> Updated at: 2016-03-07 00:02:40 UTC  
> Closed at: 2016-01-12 14:58:54 UTC  
> Url: https://github.com/boostorg/hof/issues/79  

incoherency between declaration  
  
``` c++  
template<class T, class F>  
pipable constexpr T tap(T&& x, const F& f);  
```  
  
and usage  
  
``` c++  
int r = 1 | sum(2) | tap([](int i) { std::cout << i; }) | sum(2);  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-09 16:21:08 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-146917151  

How is that incoherent?

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-09 17:28:16 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-146939465  

The `tap` function used in the pipe expression has one parameter, while the declaration has two parameters.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-09 18:38:58 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-146957216  

> The tap function used in the pipe expression has one parameter, while the declaration has two parameters.   
  
It has two since its pipable. Pipable functions can either be called as `f(x, y)` or `x | f(y)`. The example uses the piped form to show its usefulness in piped expressions as well. The signature annotates it as pipable, as well as the documentation says it pipable also.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-09 19:16:29 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-146964942  

What I see is that `tap`with two arguments return a pipable, but I don't see any `tap`function taking one argument.   
  
The implementation os  
  
```  
namespace detail {  
  
struct tap_f  
{  
    template<class T, class F>  
    constexpr T operator()(T&& x, const F& f) const  
    {  
        return f(x), fit::forward<T>(x);  
    }  
};  
  
}  
  
FIT_DECLARE_STATIC_VAR(tap, pipable_adaptor<detail::tap_f>);  
```  
  
so tap is not a template function having two arguments that return a pipable. Oh, but  the following is not correct in C++, is pseudo code, isn't it?  
  
```  
template<class T, class F>  
pipable constexpr T tap(T&& x, const F& f);  
```

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-09 20:14:46 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-146976365  

> What I see is that tapwith two arguments return a pipable  
  
It doesn't return a pipable, it returns `T`. `pipable` is just an annotation(like `constexpr`).  
  
> Oh, but the following is not correct in C++, is pseudo code, isn't it?  
  
I show all the functions as pseudo-signatures even though they are function objects because its easier to read.

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-10 11:30:39 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-147076935  

This is why I created an issue to request for a section that explain how the reference documentation is documented.  
  
I find troubling however that the signature in the documentation has two parameter and it can be used with one. More explanation are needed.

---

## Comment 7

> Username: pfultz2  
> Created at: 2016-01-12 14:58:54 UTC  
> Url: https://github.com/boostorg/hof/issues/79#issuecomment-170939187  

I added an explanation of function signatures in the library.
