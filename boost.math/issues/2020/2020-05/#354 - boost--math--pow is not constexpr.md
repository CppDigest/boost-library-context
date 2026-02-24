# #354 - boost::math::pow is not constexpr [Closed]

> Username: pabristow  
> Created at: 2020-05-18 13:42:07 UTC  
> Updated at: 2020-07-06 15:49:06 UTC  
> Closed at: 2020-07-06 15:49:06 UTC  
> Url: https://github.com/boostorg/math/issues/354  

`constexpr  int i = boost::math::pow<8>(2);`  
  
MSVC 2019 c++std=17 reports  
  
error C2131: expression did not evaluate to a constant  
  
 ` const  int i = boost::math::pow<8>(2);` //  gives the expected 256.  
  
Is there any reason why this could not be constexpr?

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-05-18 19:10:30 UTC  
> Url: https://github.com/boostorg/math/issues/354#issuecomment-630382000  

Sorry I'm gonna have to punt on this one; I have no clue what the `constexpr` rules are . . .

---

## Comment 2

> Username: cosurgi  
> Created at: 2020-06-05 15:30:52 UTC  
> Url: https://github.com/boostorg/math/issues/354#issuecomment-639566722  

Would be cool if pow was constexpr.
