# #430 - `extract_int` overflow difference between Qi and X3 [Open]

> Username: Kojoley  
> Created at: 2018-12-27 16:09:42 UTC  
> Updated at: 2025-05-10 01:47:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/430  

```cpp  
#include <boost/spirit/home/x3/support/numeric_utils/extract_int.hpp>  
#include <boost/spirit/home/qi/numeric/numeric_utils.hpp>  
#include <iostream>  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
    namespace qi = boost::spirit::qi;  
  
    char const* s = "70000", * it = s, * const end = s + std::strlen(s);  
    short x;  
    std::cout << std::boolalpha;  
    std::cout << "qi:\n";  
    x = -1; it = s; std::cout << qi::extract_int<short, 10, 1, -1>::call(s, end, x) << ' ' << x << '\n';  
    x = -1; it = s; std::cout << qi::extract_int<short, 10, 1, 5>::call(s, end, x) << ' ' << x << '\n';  
    std::cout << "x3:\n";  
    x = -1; it = s; std::cout << x3::extract_int<short, 10, 1, -1>::call(s, end, x) << ' ' << x << '\n';  
    x = -1; it = s; std::cout << x3::extract_int<short, 10, 1, 5>::call(s, end, x) << ' ' << x << '\n';  
}  
```  
Output:  
```  
qi:  
false 7000  
false 7000  
x3:  
false -1  
true 7000  
```  
https://wandbox.org/permlink/TnmsMtZcTMvFAfgr  
  
Is this intended? From my point of view all the parsers must fail and even output variable be untouched (`false -1` on all cases).

---

## Comment 1

> Username: djowel  
> Created at: 2018-12-28 03:14:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/430#issuecomment-450280459  

I think it's documented ( somewhere :-) )  that the attribute value is undefined on a failed parse, for the sake of efficiency. OTOH, if it won't affect efficiency, then the qi behavior is preferred.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-12-28 11:34:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/430#issuecomment-450345699  

> I think it's documented ( somewhere :-) ) that the attribute value is undefined on a failed parse, for the sake of efficiency.  
  
That's understandable and I fully agree.  
  
But my question was why X3 succeeds parsing with `MaxDigits=5`? Is this intended or a regression?

---

## Comment 3

> Username: djowel  
> Created at: 2018-12-28 14:25:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/430#issuecomment-450366987  

Oh duh. Looks like a regression. Not sure yet.

---

## Comment 4

> Username: saki7  
> Created at: 2025-05-10 01:47:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/430#issuecomment-2868212754  

I'm not sure if this persists on current codebase.
