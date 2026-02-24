# #88 - Compile error with MSVC when including `<complex.h>` first [Closed]

> Username: mborland  
> Created at: 2023-12-21 12:56:05 UTC  
> Updated at: 2023-12-21 23:38:31 UTC  
> Closed at: 2023-12-21 23:38:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/88  

Got a similar error from Boost.Math reported by Scipy:  
  
```  
In file included from ..\scipy\_lib\boost_math\include\boost/math/policies/policy.hpp:11:  
..\scipy\_lib\boost_math\include\boost/math/tools/mp.hpp(171,34): error: expected parameter declarator  
  171 | template<typename L, std::size_t I>  
      |                                  ^  
C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt\complex.h(63,20): note: expanded from macro 'I'  
   63 | #define I          _Complex_I  
```  
  
Minimal reproducer: https://godbolt.org/z/KavdnaYMf  
  
The draft C++ ISO states: `The header <complex.h> behaves as if it simply includes the header <complex>` so I filed an issue against MSVC STL as well.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-21 15:28:06 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866489454  

Do you want me to do something about this, and if so, what?

---

## Comment 2

> Username: mborland  
> Created at: 2023-12-21 15:30:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866494785  

When I get the logic in math right I can port it here. Linked PR: https://github.com/boostorg/math/pull/1060

---

## Comment 3

> Username: pdimov  
> Created at: 2023-12-21 15:42:58 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866521189  

`#pragma push_macro` sounds like it might work, yes.  
  
But I notice that the error is coming from your own `mp.hpp` and not anything here?

---

## Comment 4

> Username: mborland  
> Created at: 2023-12-21 15:55:09 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866548383  

It is my own derivative and reduced version of mp11. Your version is susceptible to the same error, and I was going to pull mp11 into odeint to replace MPL.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-12-21 18:29:40 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866762804  

Looks like this is only an issue under C++14, but not under C++17 and above (which warns that `<ccomplex>` is deprecated.)

---

## Comment 6

> Username: pdimov  
> Created at: 2023-12-21 19:27:40 UTC  
> Url: https://github.com/boostorg/mp11/issues/88#issuecomment-1866823027  

Also, looks like it's an issue for GCC (libstdc++) up to 7 as well.
