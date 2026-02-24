# #594 - Polution of global namespace with "mp" [Closed]

> Username: jzmaddock  
> Created at: 2021-03-30 18:04:02 UTC  
> Updated at: 2021-05-02 18:20:03 UTC  
> Closed at: 2021-05-02 18:20:03 UTC  
> Url: https://github.com/boostorg/math/issues/594  

@mborland : Apologies I should have spotted this before, but there are several places where `namespace mp = boost::math::tools::meta_programming;` occurs at global scope: this is a big no-no, everything should be contained with boost::math, just moving the namespace alias inside our namespaces should do the trick I hope!  
  
I just hit this while testing a Multiprecision bug report which used `mp` as an alias for `boost::multiprecision` and clashed.

---

## Comment 1

> Username: mborland  
> Created at: 2021-03-30 18:21:21 UTC  
> Url: https://github.com/boostorg/math/issues/594#issuecomment-810477342  

Locally moving it into the boost::math namespace worked fine. The were both included in the giant policy PR.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-05-02 17:03:51 UTC  
> Url: https://github.com/boostorg/math/issues/594#issuecomment-830839550  

@mborland can this be closed now, I think it's fixed?

---

## Comment 3

> Username: mborland  
> Created at: 2021-05-02 17:07:40 UTC  
> Url: https://github.com/boostorg/math/issues/594#issuecomment-830840048  

This can be closed
