# #279 - Error: did not match C++ signature [Open]

> Username: dickreuter  
> Created at: 2019-11-30 19:53:06 UTC  
> Updated at: 2019-11-30 20:41:30 UTC  
> Url: https://github.com/boostorg/python/issues/279  

I'm calling a pyd as follows:  
`equity` = calculator.montecarlo({"3H", "3S"}, {"8S", "4S", "QH", "8C", "4H"}, 2, 10000) `  
  
but it complains that the signature is just a set but not a set of strings:  
```  
  
EquityCalculatorMontecarlo.montecarlo(set, set, int, int)  
did not match C++ signature:  
    montecarlo(class std::set<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >, class std::set<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >, int, int)  
  
Any suggestions what I can do about it?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-11-30 20:10:08 UTC  
> Url: https://github.com/boostorg/python/issues/279#issuecomment-560017013  

You haven't provided any code (such as your EquityCalculatorMontecarlo sources), so I can only guess: It looks like you are wrapping a function taking `std::set<...>` arguments, and you calling it with Python `set` arguments. Have you defined converters for those ? Boost.Python doesn't know the C++ `set` template (much less any particular template instances such as `std::set<std::string>`. You have to define those yourself.

---

## Comment 2

> Username: dickreuter  
> Created at: 2019-11-30 20:20:07 UTC  
> Updated at: 2019-11-30 20:20:49 UTC  
> Url: https://github.com/boostorg/python/issues/279#issuecomment-560017834  

> Have you defined converters for those   
  
correct, I haven't. But what do you mean with 'converters'? Convert from what into what and where?  
thanks

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2019-11-30 20:24:55 UTC  
> Url: https://github.com/boostorg/python/issues/279#issuecomment-560018206  

You could define something like  
```  
using string_set = std::set<std::string>;  
class_<string_set> sset("string_set");  
```  
then add a constructor that takes a Python set, as well as a conversion operator that converts to a Python set.

---

## Comment 4

> Username: dickreuter  
> Created at: 2019-11-30 20:35:45 UTC  
> Url: https://github.com/boostorg/python/issues/279#issuecomment-560019176  

Ok, I'm a bit new to this so I don't fully understand. What I want is to make this function accessible:  
https://github.com/dickreuter/PokerEquityCalculator/blob/master/EquityCalculatorMontecarlo/Scoring.cpp#L240  
from python, so it's just a simple function. Is there any way to avoid having to wrap everything into a class with different constructors?

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2019-11-30 20:41:30 UTC  
> Url: https://github.com/boostorg/python/issues/279#issuecomment-560019598  

You need to instruct Boost.Python how the function arguments (native Python sets) are to be converted to C++ `std::set<std::string>`. How this is accomplished is explained in the Boost.Python docs at http://boostorg.github.io/python.
