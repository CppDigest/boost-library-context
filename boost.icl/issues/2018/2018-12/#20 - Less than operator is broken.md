# #20 - Less than operator is broken [Open]

> Username: Timmmm  
> Created at: 2018-12-20 16:50:00 UTC  
> Updated at: 2019-01-24 10:48:22 UTC  
> Url: https://github.com/boostorg/icl/issues/20  

From `interval.hpp`:  
  
```  
//- operator < -----------------------------------------------------------------  
template<class Type>  
typename boost::enable_if<is_interval<Type>, bool>::type  
operator < (const Type& left, const Type& right)  
{  
    if(icl::is_empty(left))  
        return !icl::is_empty(right);  
    else  
        return lower_less(left,right)  
            || (lower_equal(left,right) && upper_less(left,right));  
}  
```  
  
This does not seem at all right. Consider this code and its results (the comments).  
  
```  
#include <iostream>  
#include <boost/icl/interval.hpp>  
  
int main()  
{  
    const auto e0 = boost::icl::interval<int>::right_open(0, 0);  
    const auto e4 = boost::icl::interval<int>::right_open(4, 4);  
    const auto i2 = boost::icl::interval<int>::right_open(2, 3);  
    const auto i6 = boost::icl::interval<int>::right_open(6, 7);  
      
    std::cout << (e0 < e4) << "\n"; // false  
    std::cout << (e4 < e0) << "\n"; // false  
    std::cout << (i2 < i6) << "\n"; // true  
    std::cout << (i6 < i2) << "\n"; // false  
    std::cout << (e0 < i2) << "\n"; // true  
    std::cout << (i2 < e0) << "\n"; // false  
    std::cout << (e4 < i2) << "\n"; // true  
    std::cout << (i2 < e4) << "\n"; // true  
}  
```  
  
There are various WTFs in those results. Obviously the basic invariant `(a < b) == !(b < a)` is violated twice, and also the last 4 results are clearly inconsistent.  
  
Also, I think the basic idea that empty sets are treated specially is wrong, because it means you can't use them for things like `interval_set<>::lower_bound()`. I think the correct implementation would just be:  
  
```  
//- operator < -----------------------------------------------------------------  
template<class Type>  
typename boost::enable_if<is_interval<Type>, bool>::type  
operator < (const Type& left, const Type& right)  
{  
    return lower_less(left,right) || (lower_equal(left,right) && upper_less(left,right));  
}  
```  
  
But maybe there is code that relies on the current broken behaviour?

---

## Comment 1

> Username: Timmmm  
> Created at: 2019-01-24 10:48:22 UTC  
> Url: https://github.com/boostorg/icl/issues/20#issuecomment-457153343  

Looking at this again, it looks like it is part of an attempt to make every empty interval equal to each other. For example the equality operator is:  
  
```  
template<class Type>  
typename boost::enable_if<is_interval<Type>, bool>::type  
operator == (const Type& left, const Type& right)  
{  
    return (icl::is_empty(left) && icl::is_empty(right))  
        || (lower_equal(left,right) && upper_equal(left,right));  
}  
```  
  
A noble goal but I think this just leads to more WTFy behaviour than it prevents. For example:  
  
```  
void expand(boost::icl::interval<int>::type &ival) {  
   ival = {ival.lower(), ival.upper() + 1};  
}  
  
    auto e0 = boost::icl::interval<int>::right_open(0, 0);  
    auto e4 = boost::icl::interval<int>::right_open(4, 4);  
  
    std::cout << (e0 == e4) << "\n"; // true - reasonable I guess, if a bit unexpected.  
    expand(e0);  
    expand(e4);  
    std::cout << (e0 == e4) << "\n"; // false - WTF??!  
```  
  
To avoid these issues it would be much cleaner if **empty intervals were not special-cased**.
