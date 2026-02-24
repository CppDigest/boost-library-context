# #61 - Add a section describing how the reference documentation is structured [Closed]

> Username: viboes  
> Created at: 2015-10-04 09:12:36 UTC  
> Updated at: 2016-01-12 15:11:01 UTC  
> Closed at: 2016-01-12 15:11:01 UTC  
> Url: https://github.com/boostorg/hof/issues/61  

I believe that it is important for the user to know how the specification of the reference section is structured. I will add a section to describe it.  
  
I like the way the semantic is given. This is almost the equivalent as the as if on the standard  
  
```  
assert(by(p, f)(xs...) == f(p(xs)...));  
```  
  
The signature of `by` seems to  be   
  
```  
by :: ( (T->U)  (V -> W) ) -> ((X...) -> W)   
by :: p f -> xs... -> w)   
  
```  
  
where U is convertible to V and  X is convertible to T.  
  
But I believe that it supports more than that, I don't believe the library is forcing all types in X... to be the same.   
  
I like the way the semantic is given  
  
```  
assert(by(p, f)(xs...) == f(p(xs)...));  
```  
  
`p(xs)` must be well formed, `p` should have an overload taking a type convertible from decltype(xs) for each xs.   
  
`f(p(xs)...))` must be well formed, so f should have an overload taking types V... convertible from decltype(p(xs))...  
This means that `p`  
  
Wouldn't the `by` function has a signature something like  
  
```  
by :: ( ((T->U)...)  (V... -> W) ) -> ((X...) -> W)   
```  
  
Are there any constraints on the types  T..,U..., V..., X..., W? Must all these types by CopyConstructibles?  
What the library can control at compile time?  
  
E.g. would the following compile ?  
  
```  
using foo = pair<int, string>;  
using bar = pair<string, float>;  
int baz(int, float);  
  
foo a;   
bar b;  
//...  
auto x = fit::by(overload(std::mem_fn(&foo::first, std::mem_fn(&bar::second), baz)(a, b);  
``  
  
Variadic signatures are difficult to follow. Giving a specific case will surely help  
  
```  
  
by :: ( (T1->U1 T2->U2)  (V1 V2 -> W) ) -> ((X1  X2) -> W)  
requires convertible<Xi, Ti> && convertible<Ui, Vi>  
  
```  
  
Could the functions be only movable?  
  
Is the result type of the function copyable, movable, .... can be stored statically at compile time, and with the help of which macro?  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:44:29 UTC  
> Url: https://github.com/boostorg/hof/issues/61#issuecomment-145444020  

I am completely lost what you are trying to say here. Please explain.
