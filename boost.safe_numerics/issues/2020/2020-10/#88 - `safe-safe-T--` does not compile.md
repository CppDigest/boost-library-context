# #88 - `safe<safe<T>>` does not compile [Closed]

> Username: akrzemi1  
> Created at: 2020-10-13 19:37:56 UTC  
> Updated at: 2021-05-23 22:19:56 UTC  
> Closed at: 2021-05-23 22:19:56 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88  

The following code does not compile neither in Clang nor in GCC:  
  
```c++  
using boost::safe_numerics::safe;  
safe<safe<int>> r {};  
std::cout << r << std::endl;  
```  
  
Even though `safe<int>` appears to be a model of `Integer`, according to the docs.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 13:36:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88#issuecomment-708406843  

Hmmm - still puzzling about this.    
  
template <  
    class T,  
    class P = native,  
    class E = default_exception_policy  
>  
using safe = safe_base<  
    T,  
    ::std::numeric_limits<T>::min(),  
    ::std::numeric_limits<T>::max(),  
    P,  
    E  
>;  
  
expanding with T = safe<int> yields:  
  
using safe = safe_base<  
    safe<int>,  
    ::std::numeric_limits<safe<int>>::min(),  
    ::std::numeric_limits<safe<int>>::max(),  
    P,  
    E  
>;  
  
and expanding safe<int> to safe_base  
  
using safe = safe_base<  
    safe_base<  
      int,  
      ::std::numeric_limits<int>::min(),  
      ::std::numeric_limits<int>::max(),  
      P,  
      E  
    >,  
    ::std::numeric_limits<safe<int>>::min(),  
    ::std::numeric_limits<safe<int>>::max(),  
    P,  
    E  
>;  
  
which should be OK  - though pretty difficult to follow.  Not that the error on my Xcode clang fail on the declaration of a friend template - another obscure case.  
  
    template<  
        class StoredX,  
        StoredX MinX,  
        StoredX MaxX,  
        class PX, // promotion polic  
        class EX  // exception policy  
    >  
    friend class safe_base;  
  
which is there because ... - don't remember.    
  
I'm going to take a break from this now as it hurts my head.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2021-05-17 22:57:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88#issuecomment-842694868  

The problem is with "min" and "max" parameters passed to `safe_base`.   
  
```c++  
template <  
    class T,  
    class P = native,  
    class E = default_exception_policy  
>  
using safe = safe_base<  
    T,  
    ::std::numeric_limits<T>::min(), // <-- this is problematic non-type parameter  
    ::std::numeric_limits<T>::max(), // <- this is problematic non-type parameter  
    P,  
    E  
>;  
```  
  
When we build type `safe<safe<int>>`, we instantiate `safe_base`'s 2nd and third parameter with  
  
* `std::numeric_limits<safe<int>>::min()`,  
* `std::numeric_limits<safe<int>>::max()`.  
  
Those are values of type `safe<int>`, and values of types other than integer scalar types are disallowed in templates until C++20 (in C++20 more things are allowed as values as template parameters).  
  
Of course, trying to fix it might not be necessary anymore, now that concept `Integer` is gone. It makes sense to say that `safe<T>` should only be used with built-in integer types.

---

## Comment 3

> Username: robertramey  
> Created at: 2021-05-18 01:49:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88#issuecomment-842761499  

I'm just close to the end of some refactoring.  Both in the coding sense and design sense.  This started the typical way - some user complained about an overload which didn't work as expected.  The Numeric concept IS used to good effect.  The Integer one is still in there and I believe it will stay. It will mean "integer like" permit shifting, etc.  The SafeNumeric concept is still in there but will not stay - perhaps to re-appear as just Safe<T>.  Of course, refining the way that overloading is supported ended up have a large ripple effect.  Now everything works again.  But there's a huge benefit.  I now see the way to correctly support and document safe operations for non-integer types (float and user types).  This will be another slog it will head in the direction I want.  
  
I haven't consider nested safe types.   I'm guessing that one of the functions of the Safe concept will be to prevent such a recursive type.  Don't know yet.  
  
As usual, questions not easily answer are usually indications of a design mistake.  Also as usual, updating the documentation exposes design mistakes.  
  
I've tried to check in my latest changes into develop.  But .... GitHub now requires a  Personal Access Token and SourceTree which I use seems to be broken in this area.   This is the kind of thing which really drives me crazy.  Hopefully, I can get this resolved in a few days and we get in sync.  
  
Thanks for you continued interest and help.  
  
Robert Ramey

---

## Comment 4

> Username: akrzemi1  
> Created at: 2021-05-18 06:05:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88#issuecomment-842870951  

There is probably no practical use case for `safe<safe<int>>`. I created it, when studying the library and trying to understand its concepts (in Generic Programming sense). Given that `safe<T>` works on `Numeric` `T`s, and that `safe<T>` produces a `Numeric` type, it should naturally follow that `safe<safe<int>>` just works.   
  
However, given that it doesn't work in the end (this is fixable), and that you are saying you plan to prevent such instantiations, it means that we actually have two concepts in the game:  
  1. The one that `safe<T>` requires of `T`s.  
  2. The one that `safe<T>` produces itself.

---

## Comment 5

> Username: robertramey  
> Created at: 2021-05-23 00:18:52 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/88#issuecomment-846480444  

I spent some time on this today. I've concluded that the current behavior is the correct/desired one.  But the error message should be more helpful.  
  
1) safe<T> needs a new type requirement - that T is an arithmetic type - std::is_arithmetic<T>  
2) safe<T> will enforce the requirement with a static_assert.  
3) and include a helpful comment such as : "if this assert fails, you're not using a primitive type as an argument for T  
  
That should do it.
