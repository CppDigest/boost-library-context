# #118 - Avoid spurious warnings when gcc's -Wconversion is used [Open]

> Username: Eelis  
> Created at: 2021-11-04 22:40:25 UTC  
> Updated at: 2021-11-05 19:25:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/118  

Consider:  
  
    #include <optional>  
    #include <boost/safe_numerics/safe_integer.hpp>  
  
    using I = boost::safe_numerics::safe<int>;  
  
    struct X { I i; };  
  
    std::optional<I> f(X x)  
    {  
        return x.i;  
    }  
  
When asked to compile the above with `-Wconversion`, `g++` gives:  
  
    t.cpp: In function ‘std::optional<boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> > > f(X)’:  
    t.cpp:10:14: warning: choosing ‘constexpr std::optional<_Tp>::optional(_Up&&) [with _Up = boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >&; typename std::enable_if<std::__and_<std::__not_<std::is_same<std::optional<_Tp>, typename std::decay<_Up>::type> >, std::__not_<std::is_same<std::in_place_t, typename std::decay<_Up>::type> >, std::is_constructible<_Tp, _Up&&>, std::is_convertible<_Up&&, _Tp> >::value, bool>::type <anonymous> = 1; _Tp = boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >]’ over ‘constexpr boost::safe_numerics::safe_base<Stored, Min, Max, P, E>::operator R() const [with R = std::optional<boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> > >; typename std::enable_if<(! boost::safe_numerics::is_safe<R>::value), int>::type <anonymous> = 0; Stored = int; Stored Min = -2147483648; Stored Max = 2147483647; P = boost::safe_numerics::native; E = boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception>]’ [-Wconversion]  
        return x.i;  
                ^  
    t.cpp:10:14: warning:   for conversion from ‘I’ {aka ‘boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> >’} to ‘std::optional<boost::safe_numerics::safe_base<int, -2147483648, 2147483647, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::throw_exception, boost::safe_numerics::ignore_exception> > >’ [-Wconversion]  
    t.cpp:10:14: note:   because conversion sequence for the argument is better

---

## Comment 1

> Username: robertramey  
> Created at: 2021-11-05 19:16:42 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/118#issuecomment-962153074  

I'm not sure that this has anything to do with safe_numerics per se but rather using implicit conversion of member variable to wrapper type.  I'll leave this here while I think about it some more.  Here is the page describing the warning for this particular case:  [https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html](url) .  I'm thinking that gcc is being overly picky here.  See "For C++, also warn for confusing overload resolution for user-defined conversions; and conversions that never use a type conversion operator".  Also checkout what other compilers do in this case.  
  
For now I think the prudent thing to do is to replace the implicit conversion with a more explicit one.  
  
```  
#include <optional>  
#include <boost/safe_numerics/safe_integer.hpp>  
  
using I = boost::safe_numerics::safe<int>;  
  
struct X { I i; };  
  
std::optional<I> f(X x)  
{  
    return std::optional<I>(x.i);  
}  
```

---

## Comment 2

> Username: Eelis  
> Created at: 2021-11-05 19:25:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/118#issuecomment-962160769  

> I'm not sure that this has anything to do with safe_numerics per se but rather using implicit conversion of member variable to wrapper type.  
  
If I use `using I = int;` or `struct I{};` instead, the warning goes away. That made me think the issue had something to do with safe_numerics, but if that's not the case, feel free to close. :)
