# #970 - `simple_continued_fraction` [Open]

> Username: Tomaqa  
> Created at: 2023-03-28 10:12:15 UTC  
> Updated at: 2023-03-28 16:23:27 UTC  
> Url: https://github.com/boostorg/math/issues/970  

Please provide this information in the [documentation](https://www.boost.org/doc/libs/1_81_0/libs/math/doc/html/math_toolkit/internals/simple_continued_fraction.html), it is available only in the source code:  
```  
// Deal with non-uniqueness of continued fractions: [a0; a1, ..., an, 1] = a0; a1, ..., an + 1].  
// The shorter representation is considered the canonical representation,  
```  
  
Also, I am disappointed that I cannot acces particular coefficients of the continued fraction, so the whole class is useless in the case one does not want to use the provided public functions.  
I want to use it for [rational approxamations](https://en.wikipedia.org/wiki/Continued_fraction#Best_rational_approximations).  
So I also need to convert it to [rational](https://www.boost.org/doc/libs/1_81_0/libs/rational/rational.html) afterwards.

---

## Comment 1

> Username: NAThompson  
> Created at: 2023-03-28 15:03:16 UTC  
> Updated at: 2023-03-28 15:03:35 UTC  
> Url: https://github.com/boostorg/math/issues/970#issuecomment-1487062730  

This seems like a reasonable ask, and not too difficult. Lemme think about how to communicate it.

---

## Comment 2

> Username: Tomaqa  
> Created at: 2023-03-28 15:12:14 UTC  
> Updated at: 2023-03-28 16:23:27 UTC  
> Url: https://github.com/boostorg/math/issues/970#issuecomment-1487079838  

I added these member functions as a workaround:  
```  
    size_t size() const noexcept { return b_.size(); }  
  
    const auto& operator [](int idx) const { return b_[idx]; }  
    auto& operator [](int idx) { return b_[idx]; }  
  
    void reserve(size_t size_) { b_.reserve(size_); }  
    void push_back(Z c) { b_.push_back(c); }  
```  
  
Although the non-const functions may cause inconsistency with the private member variable `x_`, it seems that it is not used anywhere else than in the constructor (which suggests to actually remove the member variable and use only the parameter itself /or its local copy/).  
  
Also, it is a question whether to check index bounds or not.
