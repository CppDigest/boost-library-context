# #4 - Use case for more operators; issues with operator lookup [Closed]

> Username: pdimov  
> Created at: 2021-03-28 15:51:27 UTC  
> Updated at: 2021-06-27 16:33:35 UTC  
> Closed at: 2021-06-27 16:33:35 UTC  
> Url: https://github.com/boostorg/lambda2/issues/4  

An implementation of zip_view by Barry Revzin: https://godbolt.org/z/3fjeYzoYr  
  
Demonstrates need for `++`, `--`, `+=`. Also shows that to avoid ambiguity (with mp11's `_1`), one might want to define one's own placeholder (in this case `_`), but the operators need to be in the same namespace to be found by ADL, otherwise unrelated ones block lookup.  
  
Defining the placeholders in `boost::lambda2` would place them and the operators in the same namespace, but no longer has the elegance of just using the standard-provided facilities. Ideally, the operators would need to go into `std::placeholders`, but that's technically UB.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-31 13:51:42 UTC  
> Url: https://github.com/boostorg/lambda2/issues/4#issuecomment-811085279  

Lookup problem reduced example: https://godbolt.org/z/z8oEqnTnG  
```  
namespace N {  
    struct Irrelevant { };  
    void operator+(Irrelevant, Irrelevant);  
  
    int plus_one(int i) {  
        using namespace boost::lambda2;  
        return (_1 + 1)(i);  
    }  
}  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2021-04-14 18:43:42 UTC  
> Url: https://github.com/boostorg/lambda2/issues/4#issuecomment-819748600  

https://godbolt.org/z/PxbqvbMzK

---

## Comment 3

> Username: pdimov  
> Created at: 2021-04-14 18:45:26 UTC  
> Url: https://github.com/boostorg/lambda2/issues/4#issuecomment-819749605  

Putting the operators in `std::placeholders` doesn't work either because (a) the standard placeholder types are not required to be defined there, just the variables and (b) the object returned by `std::bind` is also not in namespace `std::placeholders`. So defining own placeholders in `boost::lambda2` is the only reliable solution.
