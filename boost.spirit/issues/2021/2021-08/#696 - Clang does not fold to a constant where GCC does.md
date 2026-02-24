# #696 - Clang does not fold to a constant where GCC does [Closed]

> Username: denzor200  
> Created at: 2021-08-20 19:15:59 UTC  
> Updated at: 2021-08-24 21:12:36 UTC  
> Closed at: 2021-08-24 21:12:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/696  

Hi. I accidentally discovered that spirit is poorly optimized by the clang compiler, even in a so simple case:  
https://godbolt.org/z/Y1PhP4qEM  
Is it possible to do something about this, or is it a problem on the clang compiler side?

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-08-20 20:44:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/696#issuecomment-902947336  

> Hi. I accidentally discovered that spirit is poorly optimized by the clang compiler, even in a so simple case:  
godbolt.org/z/Y1PhP4qEM  
  
GCC folded your code to `int main() { return 100; }`, obviously it is a Clang issue that it does not do the same  
. Either GCC guessed right to inline everything and it is folded to a constant or its IPA does a very good job in this case (and I am not even sure Clang does IPA). Some amount of bloat you see in Clang is because you did not turn off debugging (`NDEBUG` macro).  
  
> Is it possible to do something about this, or is it a problem on the clang compiler side?  
  
I guess, making Spirit more constexpr would force Clang to fold more, and it should definitely possible to make utilities the example touches fully constexpr, but it could not be done to the whole Spirit.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-08-24 21:12:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/696#issuecomment-904980562  

I do not plan any actions on this. Clang should detect if a function call depends only on constant data and recursively inline the call, then it should be able to fold everything to a constant.
