# #78 Fix include guards without leading underscore [Merged]

> Username: mloskot  
> Created at: 2018-09-14 21:15:16 UTC  
> Updated at: 2018-09-15 18:07:58 UTC  
> Merged at: 2018-09-15 07:20:55 UTC  
> Closed at: 2018-09-15 07:20:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/78  

Remove leading undescore from the macro names, make them legal C++.  
  
-----  
  
Also, [Boost library requirements](https://www.boost.org/development/requirements.html#Design_and_Programming) specify:  
> Macro (gasp!) names all uppercase and begin with BOOST_.  
  
So, the clean-up is important from the review perspective as well.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-09-14 21:23:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/78#issuecomment-421488253  

[![Coverage Status](https://coveralls.io/builds/19010029/badge)](https://coveralls.io/builds/19010029)  
  
Coverage remained the same at 99.714% when pulling **ae4f87ceff09b70a3ea122caef8eec8877f496da on mloskot:ml/fix-leading-underscores** into **fe036c9739bc49ebceb6e446bd1b65e44f39fed5 on HDembinski:master**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-09-15 07:21:17 UTC  
> Url: https://github.com/boostorg/histogram/pull/78#issuecomment-421537684  

Thank you! Somehow I didn't apply that rule to the include guards...

---
