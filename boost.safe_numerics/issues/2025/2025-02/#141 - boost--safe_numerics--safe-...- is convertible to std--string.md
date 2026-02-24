# #141 - boost::safe_numerics::safe<...> is convertible to std::string? [Open]

> Username: usefulcat  
> Created at: 2025-02-27 19:26:03 UTC  
> Updated at: 2025-10-03 22:36:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141  

Is it intentional that the following code does not produce an error when compiled?  
  
```  
#include <boost/safe_numerics/safe_integer.hpp>  
#include <type_traits>  
#include <string>  
  
static_assert(std::is_convertible_v<boost::safe_numerics::safe<int>, std::string>);  
```  
  
It seems wrong, but maybe there is a reason for it?  
  
I'm trying to track down a (to me) relatively obscure problem elsewhere, and I think this may be at least related to, if not the cause of the problems I'm seeing. I checked the two most recent versions of clang and gcc and got the same behavior in all cases.

---

## Comment 1

> Username: robertramey  
> Created at: 2025-02-27 21:24:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2689143039  

what file is this code in?  I don't find it anywhere in the codebase

---

## Comment 2

> Username: usefulcat  
> Created at: 2025-02-27 21:27:48 UTC  
> Updated at: 2025-02-27 21:31:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2689149591  

The code I presented isn't in the codebase; it's an example of code that uses boost.safe_numerics. My question is whether it is intentional and/or necessary for `boost::safe_numerics::safe` types to be convertible to `std::string`. It seems wrong that `std::is_convertible<boost::safe_numerics::safe<int>, std::string>::value` should be true. Why should a `safe<int>` automatically be convertible to `std::string`?

---

## Comment 3

> Username: robertramey  
> Created at: 2025-02-27 21:48:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2689184696  

Hmmm - one would have to check the rest of the code.  If  somewhere there is a function which converts and integer or safe_integer into a string, the static_assert value would be true.  Look for code which does such a conversion.   Assuming one fines such code, I would consider it bad practice.

---

## Comment 4

> Username: usefulcat  
> Created at: 2025-02-27 21:53:58 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2689193309  

I'm afraid you misunderstand. [The following code](https://godbolt.org/z/acG5GMod3), which is a complete example, does not compile:  
```  
#include <boost/safe_numerics/safe_integer.hpp>  
#include <type_traits>  
#include <string>  
  
static_assert(std::is_convertible_v<boost::safe_numerics::safe<int>, std::string> == false);  
  
int main() {  
    return 0;  
}  
```

---

## Comment 5

> Username: usefulcat  
> Created at: 2025-02-28 04:50:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2689713495  

After further investigation, I believe this method from safe_base is the culprit:  
```  
    /////////////////////////////////////////////////////////////////  
    // casting operators for intrinsic integers  
    // convert to any type which is not safe.  safe types need to be  
    // excluded to prevent ambiguous function selection which  
    // would otherwise occur.  validity of safe types is checked in  
    // the constructor of safe types  
    template<  
        class R,  
        typename std::enable_if<  
            ! boost::safe_numerics::is_safe<R>::value,  
            int  
        >::type = 0  
    >  
    constexpr /*explicit*/ operator R () const;  
```  
If I understand this correctly, this method allows the compiler to attempt to convert an instance of safe_base to std::string, or for that matter to _any_ type that isn't itself some flavor of safe_base.  
  
If I change the enable_if as follows (and do the same for the method body in safe_base_operations.hpp), std::is_convertible no longer reports that safe_base is convertible to std::string:  
```  
    template<  
        class R,  
        typename std::enable_if<  
            ! boost::safe_numerics::is_safe<R>::value  
            && std::is_integral<R>::value,  
            int  
        >::type = 0  
    >  
    constexpr /*explicit*/ operator R () const;  
```  
This change seems reasonable, since I thought that boost::safe_numerics was only intended to be used with integral types, but I freely admit that I'm not remotely an expert in this particular area.

---

## Comment 6

> Username: robertramey  
> Created at: 2025-02-28 15:34:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-2690958904  

Looks like you've nailed it! Good work!  I have to think about the remedy though.  Actually I have to think about why that template is in there at all!   And the comment isn't particularly helpful.  I'd be inclined to comment out that code and re-run the whole test/example suite in order to figure out the true motivation for putting this in there.  Thanks for your efforts.  
  
safe numerics is currently implemented only for integral types.  I'd like to do a fine tuning/improvement so that it support floating point types as well as other arithmetic types.

---

## Comment 7

> Username: correaa  
> Created at: 2025-10-03 21:58:08 UTC  
> Updated at: 2025-10-03 21:58:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-3367330366  

I think the current behavior consistent, in terms of substitutability of `int` with `safe<int>`  
It is not an ideal situation, but that is a defect of `std::string`.  
  
```cpp  
#include <string>  
  
#include <boost/safe_numerics/safe_integer.hpp>  
  
void f(std::string);  
  
int main() {  
    static_assert(std::is_convertible_v<int, std::string>);  
  
    int a = 0;  
    // f(a);  // error, good  
    f(std::string{a});  // accepted, narrowing conversion warning  
  
    static_assert(std::is_convertible_v<boost::safe_numerics::safe<int>, std::string>);  
  
    boost::safe_numerics::safe<int> as = a;  
    // f(as);  // error, good, although deep in the library  
    f(std::string{as});  // accepted, no warning  
}  
```  
  
The only QoI issue is that 1) the error (of `f(as)` happens deep in the library). 2) The warning `-Wnarrowing` doesn't happen at all `f(std::string{as})` even with warns active.  
  
1) can be solved by adding more SFINAE conditions here  
  
```cpp  
    template<  
        class R,  
        typename std::enable_if<  
            ! boost::safe_numerics::is_safe<R>::value  
            && std::is_integral<R>::value,  
            int  
        >::type = 0  
    >  
    constexpr /*explicit*/ operator R () const;  
```  
  
I particular adding `enable_if< ... , std::is_convertible<Stored, R>, ...>, or something like it. In this case, the error will move from deep inside the library to the interface.

---

## Comment 8

> Username: usefulcat  
> Created at: 2025-10-03 22:36:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/141#issuecomment-3367431715  

I don't think this problem has anything to do with std::string. I just used that as what I thought would be an obvious example of this feature not working as intended. Here's a (hopefully) more obvious example:  
  
```  
class Foo {};  
static_assert(std::is_convertible_v<boost::safe_numerics::safe<int>, Foo> == false);  
```  
IMO that should compile (is_convertible_v should be false) but it doesn't.  
  
The problem I ran into has to do with writing custom formatters for fmtlib, and in the case of a safe_numerics::safe type, fmtlib is unable to figure out which overload (or specialization--I forget) to use because, as currently implemented, safe_numerics::safe::operator R() says that the compiler is allowed to attempt to convert it to **any** non-safe_numerics type. Which is, to say the very least, extremely surprising.  
  
Ultimately I ended up significantly reducing my usage of safe_numerics since that was the only solution I could find that didn't require me to maintain my own fork of the library.
