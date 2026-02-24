# #38 - MSVC-12 fails to generate defaulted functions [Closed]

> Username: Lastique  
> Created at: 2017-10-29 12:05:26 UTC  
> Updated at: 2018-11-04 15:04:48 UTC  
> Closed at: 2018-11-04 15:04:48 UTC  
> Url: https://github.com/boostorg/optional/issues/38  

The issue appeared in Boost.Log tests. Apparently, MSVC-12 cannot generate defaulted move constructor and assignment operator:  
  
```  
D:\teeks99-09\run\boost_root\boost/optional/optional.hpp(968) : error C2610: 'boost::optional<T>::optional(boost::optional<T> &&)' : is not a special member function which can be defaulted  
        D:\teeks99-09\run\boost_root\boost/optional/optional.hpp(1353) : see reference to class template instantiation 'boost::optional<T>' being compiled  
D:\teeks99-09\run\boost_root\boost/optional/optional.hpp(1045) : error C2610: 'boost::optional<T> &boost::optional<T>::operator =(boost::optional<T> &&)' : is not a special member function which can be defaulted  
```  
  
This results in build failures.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-10-29 12:08:00 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-340257574  

@jzmaddock As possible solution, we could define `BOOST_NO_CXX11_DEFAULTED_FUNCTIONS` for MSVC-12 in Boost.Config.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-29 16:36:09 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-340275467  

Or add `BOOST_NO_CXX11_DEFAULTED_MOVE_FUNCTIONS`.

---

## Comment 3

> Username: Lastique  
> Created at: 2017-10-29 16:48:31 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-340276292  

@pdimov I'd rather not diversify `BOOST_NO_CXX11_DEFAULTED_FUNCTIONS` for compiler-specific bugs. This bug appears to be fairly specific, I'm not aware of other compilers with the same problem.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-10-29 17:07:34 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-340277580  

It depends on the order in which support for defaulted functions and support for compiler-generated move is added to the compiler. It could be that msvc is the only one.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-29 17:18:08 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-340278299  

According to https://gcc.gnu.org/projects/cxx-status.html, defaulted functions were added in 4.4, compiler-generated move in 4.6. Not very relevant nowadays, perhaps.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2018-01-10 18:24:42 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-356691657  

I have submitted a patch to Boost.Config: https://github.com/boostorg/config/pull/205.

---

## Comment 7

> Username: Lastique  
> Created at: 2018-11-04 15:04:48 UTC  
> Url: https://github.com/boostorg/optional/issues/38#issuecomment-435677027  

Boost.Config PR has been merged, this is probably fixed.
